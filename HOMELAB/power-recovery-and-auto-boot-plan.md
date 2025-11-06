# Homelab Power Recovery and Auto-Boot Plan

This note documents a simple, reliable setup to ensure a laptop homelab:
- Shuts down cleanly after 4 hours on battery power (power outage).
- Boots automatically when AC power returns (via BIOS/UEFI setting).

The recommended approach uses a small systemd timer and script. It requires no
extra packages and survives reboots. An acpid-based alternative is included
below for completeness.

## Objectives
- Avoid battery drain and potential data corruption during long outages.
- Cleanly power off after 4 hours without AC power.
- Auto-boot when AC returns using BIOS/UEFI “Restore on AC Power Loss”.

## Prerequisites
- BIOS/UEFI setting: enable “Restore on AC Power Loss” / “AC Back” / “Power on AC”.
- Linux with systemd (most modern distros).

## Recommended: Systemd Timer (checks every minute)

Creates a small state file marking when AC was lost; shuts down after 4 hours if still on
battery. Clears the state as soon as AC returns.

1) Create the guard script

```bash
sudo tee /usr/local/sbin/power-guard.sh >/dev/null <<'EOF'
#!/usr/bin/env bash
set -euo pipefail
STATE_DIR=/var/lib/power-guard
STATE_FILE="$STATE_DIR/on_battery_since"
mkdir -p "$STATE_DIR"

# Detect AC power supply device in sysfs
AC_DIR=$(ls -d /sys/class/power_supply/* 2>/dev/null | grep -E '/(AC|ACAD|ADP|Mains)[^/]*$' | head -n1 || true)
ONLINE=$(cat "$AC_DIR/online" 2>/dev/null || echo 1)

THRESHOLD=${POWER_GUARD_MAX_SECS:-14400} # 4h = 14400s
now=$(date +%s)

if [ "$ONLINE" = "0" ]; then
  # On battery
  if [ ! -f "$STATE_FILE" ]; then
    echo "$now" > "$STATE_FILE"
    logger -t power-guard "AC lost; timer started"
  else
    start=$(cat "$STATE_FILE" || echo "$now")
    if (( now - start >= THRESHOLD )); then
      logger -t power-guard "On battery >= $((THRESHOLD/3600))h; shutting down"
      systemctl poweroff
    fi
  fi
else
  # AC restored
  if [ -f "$STATE_FILE" ]; then
    rm -f "$STATE_FILE"
    logger -t power-guard "AC restored; timer cleared"
  fi
fi
EOF
sudo chmod +x /usr/local/sbin/power-guard.sh
```

2) Create the oneshot service

```ini
sudo tee /etc/systemd/system/power-guard.service >/dev/null <<'EOF'
[Unit]
Description=Power guard: shutdown after long AC loss

[Service]
Type=oneshot
ExecStart=/usr/local/sbin/power-guard.sh
EOF
```

3) Create and enable the timer (runs every minute)

```ini
sudo tee /etc/systemd/system/power-guard.timer >/dev/null <<'EOF'
[Unit]
Description=Run power-guard every minute

[Timer]
OnBootSec=1min
OnUnitActiveSec=1min
AccuracySec=30s
Persistent=true

[Install]
WantedBy=timers.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now power-guard.timer
```

4) Verify

- Check timer is active: `systemctl list-timers | grep power-guard`
- See logs: `journalctl -u power-guard.service -S today`
- Quick test (1 minute threshold):
  ```bash
  sudo POWER_GUARD_MAX_SECS=60 /usr/local/sbin/power-guard.sh
  ```

## Test (60s Threshold End-to-End)

By default, the timer runs the script with a 4-hour threshold. For an accurate
end-to-end test, temporarily override the service environment so every run uses
60 seconds. Afterwards, remove the override to return to 4 hours.

1) Add a temporary override for 60s

```bash
sudo mkdir -p /etc/systemd/system/power-guard.service.d
printf "[Service]\nEnvironment=POWER_GUARD_MAX_SECS=60\n" \
  | sudo tee /etc/systemd/system/power-guard.service.d/override.conf
sudo systemctl daemon-reload
sudo systemctl restart power-guard.timer
```

2) Run the test on battery

- Unplug AC and confirm battery state: `grep . /sys/class/power_supply/AC/online`
- Reset state and trigger an immediate check, then watch logs:

```bash
sudo rm -f /var/lib/power-guard/on_battery_since
sudo systemctl start power-guard.service
journalctl -fu power-guard.service
```

Within ~1–2 minutes you should see a log entry like:
`power-guard: On battery >= 0h; shutting down`, and the system powers off.

3) Restore normal (4-hour) behavior

```bash
sudo rm /etc/systemd/system/power-guard.service.d/override.conf
sudo systemctl daemon-reload
sudo systemctl restart power-guard.timer
```

### Optional: Scripted Helpers (copy-paste)

Create-and-enable 60s override, restart timer, and trigger a check:

```bash
cat >/tmp/power-guard-test-60s.sh <<'EOS'
#!/usr/bin/env bash
set -euo pipefail
sudo mkdir -p /etc/systemd/system/power-guard.service.d
printf "[Service]\nEnvironment=POWER_GUARD_MAX_SECS=60\n" \
  | sudo tee /etc/systemd/system/power-guard.service.d/override.conf >/dev/null
sudo systemctl daemon-reload
sudo systemctl restart power-guard.timer
sudo rm -f /var/lib/power-guard/on_battery_since
sudo systemctl start power-guard.service
echo "Override set to 60s and service triggered. Unplug AC and tail logs with:"
echo "  journalctl -fu power-guard.service"
EOS
chmod +x /tmp/power-guard-test-60s.sh
/tmp/power-guard-test-60s.sh
```

Cleanup and restore 4-hour default:

```bash
cat >/tmp/power-guard-restore-default.sh <<'EOS'
#!/usr/bin/env bash
set -euo pipefail
sudo rm -f /etc/systemd/system/power-guard.service.d/override.conf
sudo systemctl daemon-reload
sudo systemctl restart power-guard.timer
echo "Restored default (4h)."
EOS
chmod +x /tmp/power-guard-restore-default.sh
/tmp/power-guard-restore-default.sh
```

## Alternative: acpid (event-based)

Schedules a shutdown 4h after AC loss; cancels it when AC returns.

```bash
sudo apt install -y acpid
sudo systemctl enable --now acpid

sudo tee /etc/acpi/events/ac_adapter >/dev/null <<'EOF'
event=ac_adapter
action=/etc/acpi/ac-power.sh
EOF

sudo tee /etc/acpi/ac-power.sh >/dev/null <<'EOF'
#!/bin/sh
set -eu
LOGTAG="ac-power-guard"
AC_PATH="$(ls -d /sys/class/power_supply/* 2>/dev/null | grep -E '/(AC|ACAD|ADP|Mains)[^/]*$' | head -n1)"
ONLINE="$(cat "$AC_PATH/online" 2>/dev/null || echo 1)"
if [ "$ONLINE" = "0" ]; then
  logger -t "$LOGTAG" "AC lost: scheduling shutdown in 4h"
  shutdown -h +240 "Power outage: auto shutdown in 4 hours unless AC returns"
else
  logger -t "$LOGTAG" "AC restored: canceling scheduled shutdown"
  shutdown -c || true
fi
EOF
sudo chmod +x /etc/acpi/ac-power.sh
sudo systemctl restart acpid
```

## Auto-Boot on Power Return

- Enable in BIOS/UEFI: “Restore on AC Power Loss” / “AC Back” / “Power on AC”.
- Optional: keep logs persistent for post-mortem: `sudo mkdir -p /var/log/journal`.

## Troubleshooting
- Confirm AC detection: `grep . /sys/class/power_supply/*/online 2>/dev/null`
- View last actions: `journalctl -t power-guard -S yesterday`
- Timer not running: `systemctl status power-guard.timer`
- Adjust threshold: set `POWER_GUARD_MAX_SECS` in environment for manual runs.

## Checklist
- BIOS “Restore on AC Power Loss” enabled.
- `power-guard.timer` active and logging.
- Test: unplug AC for >60s with test override; verify clean shutdown.
- After power returns, machine auto-boots and services come up.
