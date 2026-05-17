# Review of `planning/PLAN.md`

## Findings

1. High: core implementation decisions are still parked in Section 13 instead of being resolved in the normative sections.
Section 13 correctly identifies major open questions, but Sections 6-11 still leave them undecided: DB init timing, dynamic SSE behavior after watchlist changes, simulator meaning of "daily change %", chat history cap, non-watchlist trading, `portfolio_snapshots` formula, sparkline behavior after reconnect, and the concrete LiteLLM integration pattern. As written, different agents can implement different answers and still claim compliance.
Refs: `planning/PLAN.md:172-178`, `planning/PLAN.md:184-190`, `planning/PLAN.md:226-230`, `planning/PLAN.md:286-343`, `planning/PLAN.md:353-368`, `planning/PLAN.md:455-481`

2. High: the API contract is too vague for frontend and backend work to proceed independently.
Section 8 lists endpoints, but almost none of the request or response shapes are defined. Section 6 says SSE events contain ticker, price, previous price, timestamp, and change direction, but does not define exact JSON keys or whether events are per-ticker or batched. Without concrete payload examples, the frontend agent and backend agent will invent different contracts.
Refs: `planning/PLAN.md:172-178`, `planning/PLAN.md:247-276`

3. Medium: the environment-variable spec is internally inconsistent and incomplete.
Section 5 marks `OPENROUTER_API_KEY` as required, but Section 9 explicitly says `LLM_MOCK=true` supports development and tests without an API key. Section 11 also relies on `STATIC_FILES_DIR`, but Section 5 does not document it at all. The env-var section should be the single source of truth.
Refs: `planning/PLAN.md:121-138`, `planning/PLAN.md:338-343`, `planning/PLAN.md:394-415`

4. Medium: ticker input rules are underspecified.
The watchlist and trade APIs accept free-text tickers, while the schema uses raw `TEXT` plus uniqueness constraints. The plan never states normalization or validation rules such as `trim + uppercase`, allowed characters, max length, or what happens when the user trades a ticker not already in the watchlist. This will create inconsistent storage and lookup behavior unless the contract is written down now.
Refs: `planning/PLAN.md:201-215`, `planning/PLAN.md:257-266`, `planning/PLAN.md:358`, `planning/PLAN.md:471-472`

5. Medium: the portfolio-history contract is incomplete.
`portfolio_snapshots` is central to the P&L chart, but the plan does not define the exact `total_value` formula in the normative schema section, does not say what happens when a needed live price is missing, and does not define whether an initial snapshot is created at startup. Without that, the chart may be empty for the first 30 seconds on a fresh launch, which conflicts with the immediate dashboard experience promised earlier.
Refs: `planning/PLAN.md:15-20`, `planning/PLAN.md:226-230`, `planning/PLAN.md:356`, `planning/PLAN.md:474-475`

6. Low: two UX statements currently conflict with the implementation notes.
Section 2 says the start script opens a browser automatically, but Section 11 only guarantees printing the URL on Windows and says auto-open is optional on macOS/Linux. The watchlist also promises "daily change %" even though the simulator has no previous-close reference. These are easy fixes, but they should be made before agents start implementing UI behavior from the plan.
Refs: `planning/PLAN.md:15`, `planning/PLAN.md:353`, `planning/PLAN.md:398-405`, `planning/PLAN.md:465-466`

## Recommendation

Before implementation continues, convert every open question in Section 13 into an explicit rule inside Sections 5-11, then add:

- one SSE payload example
- one response example for each major REST endpoint
- a complete env-var list in Section 5
- explicit ticker normalization and validation rules

Until that is done, the main risk is not technical difficulty but spec drift between agents.
