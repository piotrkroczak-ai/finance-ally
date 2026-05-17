# FinAlly — Project Log

Chronological record of significant decisions, architectural changes, and milestones.

---

## 2026-05-18 — Session: Decisions resolved before §7 implementation

### Context

All major open questions blocking §7 (Database) and §8 (API) implementation were tranched before any new code was written. Decisions consolidated into `planning/PLAN.md` in a single batch.

### Decisions

| Question | Decision | PLAN section updated |
|---|---|---|
| §13 Q1 — Lazy DB init timing | Startup-only via FastAPI lifespan event (eliminates race) | §7 "SQLite Initialization at Startup" |
| §13 Q2 — SSE behaviour on watchlist change | Server watches watchlist; client never reconnects | §6 "SSE Streaming" |
| §13 Q3 — Daily change % with simulator | New `daily_closes` table; shutdown hook persists last prices; first session shows `N/A` | §7 schema, §8 `GET /api/watchlist` |
| §13 Q4 — Chat history limit | 20 last messages (mixed roles, not pairs) | §9 step 2 |
| §13 Q5 — Trade ticker not in watchlist | Reject with HTTP 400; no auto-add | §8 `POST /api/portfolio/trade` rules |
| §13 Q6 — `total_value` formula + fallback | `cash + Σ(qty × current_price)`; fallback to `seed_prices.py` if cache empty; never `avg_cost` | §7 portfolio_snapshots |
| §13 Q7 — Sparkline loss on reconnect | Accept the loss; no `/api/prices/history` endpoint | §6 SSE Streaming note |
| §13 Q8 — Concrete LLM integration | Deferred to §9 implementation; confirmed direction: LiteLLM + free-tier OpenRouter model + JSON schema | §13 (kept open) |
| REVIEW #3 — Env vars consistency | `OPENROUTER_API_KEY` now explicitly conditional on `LLM_MOCK`; `STATIC_FILES_DIR` documented | §5 |
| REVIEW #4 — Ticker normalization | `trim()` + `upper()` + regex `^[A-Z]{1,5}$`; applied at every entry point | §7 "Ticker Normalization" |
| REVIEW #2 — API contracts | Request/response JSON examples added for every endpoint; SSE event payload specified | §8 entire section |
| Initial portfolio snapshot | Triggered on first cache fill (avoids 30s empty-chart gap on cold start) | §7 portfolio_snapshots |
| `daily_closes` write trigger | Shutdown only (lifespan shutdown hook). No periodic write. | §7 daily_closes |
| `daily_closes` schema | One row per ticker, PK = ticker, overwrite on each shutdown (no history) | §7 schema |

### Open

- §13 Q8 (concrete LiteLLM model string and structured-output call pattern) — to be resolved at the start of §9 implementation.

### Next step

Implement §7 increment 1: `backend/schema/init.py` with DDL for all 7 tables (user_profile, watchlist, positions, trades, portfolio_snapshots, daily_closes, chat_messages) + idempotent `init_db(path)` function + pytest coverage proving fresh-init, seed correctness, and idempotence. No FastAPI wiring yet.

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
