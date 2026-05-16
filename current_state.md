# FinAlly — Current State

> This file is updated at the end of each working session. It is the fastest way to resume work after a restart.
> At the end of each session, ask Claude: "Should we update current_state.md?"

---

## Last updated: 2026-05-16

## What is built and working

- **Market data subsystem** (§6) — fully implemented
  - `SimulatorDataSource`: GBM price generation, correlated moves, random events
  - `MassiveDataSource`: Polygon.io REST polling, same interface
  - `PriceCache`: thread-safe in-memory store with version counter
  - SSE endpoint: `GET /api/stream/prices` streams all tickers at ~500ms
  - Demo script: `uv run market_data_demo.py` shows a live terminal dashboard

## What is in progress

- Nothing currently in progress (session ended after planning/documentation work)

## What is next (priority order)

1. **Update PLAN.md** to reflect no-Docker local development (Section 11 rewrite)
2. **Backend core** (§7–§8): database init, schema, portfolio API, watchlist API, trade execution
3. **LLM integration** (§9): chat endpoint, structured output, auto-execution of trades
4. **Frontend** (§10): Next.js app with watchlist, charts, portfolio heatmap, trade bar, chat panel
5. **Start scripts**: `scripts/start_windows.ps1` and `scripts/start_mac.sh` (no Docker)
6. **E2E tests** (§12): Playwright tests in `tests/`

## Open decisions / blockers

- PLAN.md Section 11 needs rewriting for no-Docker local dev (discussed in session, not yet done)
- Charting library not decided: Lightweight Charts vs Recharts (see `planning/choice_tech.md`)
- Chat history limit not specified (see PLAN.md §13 review question #4)
- Daily change % behaviour with simulator undefined (see PLAN.md §13 review question #3)
- Static files path convention: FastAPI should read `STATIC_FILES_DIR` env var (agreed, not yet implemented)

## Key file locations

| What | Where |
|------|-------|
| Project plan | `planning/PLAN.md` |
| Tech choices | `planning/choice_tech.md` |
| Change log | `planning/logs.md` |
| Bug tracker | `planning/bugs.md` |
| Test results | `tests/tests.md` |
| Backend entry point | `backend/app/` |
| Market data module | `backend/app/market/` |
| DB schema | `backend/db/` |
| Env vars | `.env` (gitignored), `.env.example` |

## Environment

- OS: Windows 10 (development machine)
- Docker: NOT installed (will be added later on Linux)
- Python: 3.12+ with uv
- Node: required for frontend build
- API keys: `OPENROUTER_API_KEY` present in `.env`
