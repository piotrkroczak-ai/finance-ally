# FinAlly — Test Results Log

Records all tests run at each development step, their outcome, and any fixes applied.

Sections map to plan sections (§N) so progress is traceable.

---

## Format

Each entry:
- **Date**: when the test was run
- **Type**: Unit / Integration / E2E / Manual
- **Plan section**: which part of the spec this covers
- **Result**: Pass / Fail / Skipped
- **Notes**: what was observed; fix applied if failed

---

## §6 Market Data — Simulator & SSE (COMPLETED)

> Market data subsystem is implemented. See `planning/MARKET_DATA_SUMMARY.md` for full details.

| Date | Test | Type | Result | Notes |
|------|------|------|--------|-------|
| Pre-session | GBM price generation | Unit | Pass | Valid prices, correct drift/volatility |
| Pre-session | Correlated ticker moves | Unit | Pass | Tech stocks move together as expected |
| Pre-session | Massive API response parsing | Unit | Pass | Conforms to abstract interface |
| Pre-session | SSE stream (`/api/stream/prices`) | Manual | Pass | EventSource receives updates at ~500ms |
| Pre-session | PriceCache thread safety | Unit | Pass | Version counter increments correctly |

---

## §7 Database — Schema & Lazy Init

_Not yet tested. Will be filled in during backend implementation._

---

## §8 API Endpoints — Portfolio, Watchlist, Chat

_Not yet tested. Will be filled in during backend implementation._

---

## §9 LLM Integration

_Not yet tested. Will be filled in during backend implementation._

---

## §10 Frontend

_Not yet tested. Will be filled in during frontend implementation._

---

## §12 E2E Tests (Playwright)

_Not yet run. Infrastructure not yet created._

---

_Add new rows as tests are run. Keep sections in plan order._
