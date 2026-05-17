# FinAlly — Project Log

Chronological record of significant decisions, architectural changes, and milestones.

---

## 2026-05-17 — Session: Repo cleanup before sandbox clone

### Claude instructions restructured

- `.claude/instructions.md` nettoyé : suppression des infos personnelles (nom, username Windows, chemins PC), suppression des sections PC-specific
- `.claude/local-setup.md` créé : contient toute la config PC perso (chemins UV, disques C:/G:) — gitignored
- `.gitignore` mis à jour : ajout de `.claude/local-setup.md`
- `CLAUDE.md` mis à jour : ajout Working Session Protocol, Project Structure Reference, référence à `instructions.md`
- `planning/PLAN.md` mis à jour : session open price dans SSE spec, Lightweight Charts choisi (résolution du blocker), note Docker locale

### Decisions resolved

- **Charting library** : Lightweight Charts (TradingView) — choix acté dans PLAN.md §10
- **PLAN.md Section 11** : réécriture no-Docker complète (faite en session précédente, confirmée today)

---

## 2026-05-16 — Session 1: Initial planning review & architecture change

### Docker removed as primary runtime

**Decision**: Docker is not available on the current development machine (Windows). Docker will be introduced later when deploying to a Linux machine. The plan has been updated to use direct local execution instead.

**Previous approach**: Single Docker container running everything — multi-stage Dockerfile builds the frontend, installs Python deps, and starts uvicorn.

**New approach for local development**:
- Frontend: `cd frontend && npm install && npm run build` — outputs static export to `frontend/out/`
- Backend: `cd backend && uv sync && uv run uvicorn app.main:app --port 8000`
- FastAPI serves static files from a configurable path via `STATIC_FILES_DIR` env var (default: `../frontend/out/`)
- SQLite at `db/finally.db` in the project root — local file, no volume mount needed
- Start scripts: PowerShell (`scripts/start_windows.ps1`) and bash (`scripts/start_mac.sh`) replace Docker wrappers

**Future Docker path (Linux deployment)**:
- Dockerfile will set `STATIC_FILES_DIR=/app/static/` and mount `db/` as a volume
- No code changes required — only env var differs

**Prerequisites for local dev**: Node.js + npm, Python 3.12+, uv

### PLAN.md review completed

Added Section 13 to `planning/PLAN.md` with 8 clarification questions and 5 simplification opportunities identified during document review.

### Folder naming collisions resolved

- Root `tests/` renamed to `e2e/` — prevents confusion with `backend/tests/` (pytest) and future `frontend/tests/` (Vitest). Three test folders with the same name would have caused sub-agent misdirection.
- `backend/db/` does not yet exist; will be created as `backend/schema/` when backend implementation begins, preventing collision with top-level `db/` (runtime SQLite location).

### Documentation structure established

Created: `planning/logs.md`, `planning/bugs.md`, `planning/choice_tech.md`, `e2e/tests.md`, `current_state.md`

---

_Add new entries at the top of this list (newest first)._

## 2026-05-16 23:19 - Add stop hook logging

- updated `.claude/settings.json` to add an async `Stop` hook that runs `codex exec` and checks `git status --short` plus `git diff HEAD --stat`
- decided to place the automation in project `.claude/settings.json` so session-end logging is handled by the hook instead of manually
