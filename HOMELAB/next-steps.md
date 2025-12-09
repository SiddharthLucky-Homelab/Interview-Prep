# Homelab Next Steps

- Implement the power guard (systemd timer) as described in [[Homelab Power Recovery and Auto-Boot Plan]].
- Verify behavior:
  - Unplug AC and confirm shutdown occurs after the configured threshold (use a 60s test override first).
  - Reconnect AC and confirm the machine auto-boots (BIOS “Restore on AC Power Loss”).
  - Check logs: `journalctl -u power-guard.service -S today`.
- After verification: create `HOMELAB/scripts/` and add ready-to-run helpers:
  - `power-guard.sh` (managed copy for `/usr/local/sbin`).
  - `install-power-guard.sh` (installs units, reloads systemd, enables timer).
  - `acpid-variant.sh` (optional alternative flow).
- Optional follow-ups:
  - Add health notifications (webhook ping) on shutdown trigger.
  - Persist logs (`/var/log/journal`) for post-mortem after power events.

