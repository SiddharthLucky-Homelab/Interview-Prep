# Repository Guidelines

## Project Structure & Module Organization
- Notes live at the root (e.g., `README.md`, `Welcome.md`).
- Obsidian settings are under `.obsidian/` — avoid editing unless intentional.
- Git help lives in `git-instructions.md`.
- Organize topics with folders (e.g., `frontend/`, `backend/`, `algorithms/`). Place images in `assets/` and link relatively.

## Build, Test, and Development Commands
- Open vault: launch Obsidian and open this folder.
- Search notes: `rg -n "keyword"` for fast full‑text search.
- Optional lint: `markdownlint **/*.md` if installed.
- For code subprojects (when added): run their local commands (e.g., `npm test`, `pytest`) from that subfolder.

## Coding Style & Naming Conventions
- One `#` H1 per note; Title Case for H1.
- Filenames: kebab-case and descriptive (e.g., `event-loop.md`).
- Use `-` for bullets; fence code blocks with language (e.g., ```ts).
- Indentation: 2 spaces; keep lines ~100 chars.
- Prefer wiki links `[[Note Title]]` or stable relative paths.

## Testing Guidelines
- For code subprojects: place tests in `tests/` or `__tests__/`.
- Name tests `*.test.*` or `test_*.py` (language‑appropriate).
- Aim for ≥80% coverage where tooling exists; keep tests isolated and fast.

## Commit & Pull Request Guidelines
- Conventional Commits (`docs:`, `feat:`, `fix:`, `chore:`). Imperative, concise subjects.
- Small, scoped diffs; avoid incidental `.obsidian/` changes.
- PRs: include purpose, linked issues, and screenshots if visual.


