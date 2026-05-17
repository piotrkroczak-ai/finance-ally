# FinAlly Project - the Finance Ally

## Start of Session - Read These First

@current_state.md

---

## Claude Instructions

This project uses `.claude/instructions.md` to provide Claude with consistent coding guidelines and development context. These instructions are automatically loaded when working in this project and ensure consistent AI assistance across all development tasks.

For details on how Claude will assist you, see `.claude/instructions.md`.

---

## Project Documentation

All project documentation is in the `planning` directory.

| File | Purpose |
|------|---------|
| `planning/PLAN.md` | Full project specification |
| `planning/logs.md` | Chronological log of decisions and architectural changes |
| `planning/bugs.md` | Bug tracker — symptoms, root causes, resolutions |
| `planning/choice_tech.md` | Technology choices with pros/cons |
| `planning/MARKET_DATA_SUMMARY.md` | Summary of the completed market data component |
| `e2e/tests.md` | Test results logged per plan section |

Consult `planning/archive/` for full market data implementation details if needed.

---

## Working Session Protocol

### At the Start

1. Read `@current_state.md` (above)
2. Claude loads `.claude/instructions.md` automatically
3. Begin work on your task

### At the End

Before closing, ask Claude: "Should we update `current_state.md`?"

This keeps the project state synchronized and documentation current.

---

## Project Structure Reference

```
finally/
├── .claude/
│   └── instructions.md              (Claude's coding guidelines)
├── planning/
│   ├── PLAN.md                      (Specification)
│   ├── logs.md                      (Session decisions)
│   ├── bugs.md                      (Issues & fixes)
│   ├── choice_tech.md               (Tech decisions)
│   ├── MARKET_DATA_SUMMARY.md       (Component status)
│   └── archive/                     (Historical details)
├── e2e/
│   └── tests.md                     (Test results)
├── backend/
├── skills/
├── agents/
├── commands/
├── .github/
├── src/
├── CLAUDE.md                        (This file)
├── CLAUDE.md                        (Claude instructions reference)
├── current_state.md                 (Session state)
└── README.md                        (Project overview)
```

---

@planning/PLAN.md