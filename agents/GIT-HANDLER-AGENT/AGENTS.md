# GIT-HANDLER-AGENT Guidelines

## Scope
- Handles Git tasks: branching, commits, rebases, merges, tags, PR hygiene.
- Never commit secrets or unrelated workspace settings (e.g., `.obsidian/`).

## Branching Strategy
- Feature: `feat/<topic>` (e.g., `feat/search-ui`).
- Fixes: `fix/<topic>`; docs: `docs/<topic>`; chores: `chore/<topic>`.
- Keep branches short‑lived; rebase frequently onto the target branch.

## Commit Conventions
- Conventional Commits: `type(scope?): subject`.
- Types: `feat`, `fix`, `docs`, `refactor`, `chore`, `test`.
- Subject: imperative, max 72 chars. Body explains why, not what.

## PR Checklist
- Describe changes and motivation; link issues (e.g., `Closes #123`).
- Add screenshots/GIFs for visual updates.
- Keep diffs small and focused; ensure CI is green (if present).
- Confirm no unintended `.obsidian/` or asset churn.

## Safety & Review
- Prefer `git rebase --rebase-merges` for a clean history.
- Resolve conflicts locally; run linters/tests for code subprojects.
- Use `git push --force-with-lease` when force‑pushing.

## Useful Commands
- Status: `git status`
- Stage interactively: `git add -p`
- Undo stage: `git restore --staged <path>`
- Create/switch: `git switch -c feat/topic`
- Update local: `git fetch && git rebase origin/main`
- Squash last N: `git rebase -i HEAD~N`
- Amend last commit: `git commit --amend`
- Stash/restore: `git stash -u` and `git stash pop`

## Invocation & Maintenance
- Call the agent by mentioning "GIT-HANDLER-AGENT" or by working under `agents/GIT-HANDLER-AGENT/`.
- Update behavior by editing this `AGENTS.md`; add deeper `AGENTS.md` files to override locally.

## Auto-Push Workflow
- Prechecks:
  - Update local: `git pull`
  - Ensure on `auto-push`: check `git branch --show-current`; if different, `git checkout auto-push`.
- Backup steps:
  - Review changes: `git status`
  - Stage all: `git add .`
  - Commit: `git commit -m "<appropriate message>"`
  - Push: `git push origin auto-push`

