# FinAlly — Technology Choices

Documents every significant technology decision: what was chosen, why, trade-offs, and how the choice may evolve.

---

## Backend

### Runtime: Python 3.12 + FastAPI + uvicorn

| | |
|---|---|
| **Chosen** | Python 3.12, FastAPI, uvicorn (ASGI server) |
| **Alternatives considered** | Node/Express, Go/Gin, Django |

**Pros**:
- FastAPI has native async support, automatic OpenAPI docs, and Pydantic for structured validation
- Python is the natural home for LLM/AI integrations (LiteLLM, numpy for GBM simulation)
- uvicorn is lightweight and production-grade
- Excellent SSE support via `StreamingResponse`

**Cons**:
- Python's GIL limits true parallelism (mitigated by async I/O and a single-user design)
- Slightly more boilerplate than a pure Node backend for simple JSON APIs

---

### Package manager: uv

| | |
|---|---|
| **Chosen** | uv |
| **Alternatives considered** | pip + venv, Poetry, pipenv |

**Pros**:
- Dramatically faster than pip for installs
- Produces a reproducible lockfile (`uv.lock`)
- Manages virtual environments automatically
- Modern standard — what students should learn

**Cons**:
- Newer tool; less StackOverflow coverage than pip/Poetry

---

### Database: SQLite

| | |
|---|---|
| **Chosen** | SQLite (single file: `db/finally.db`) |
| **Alternatives considered** | PostgreSQL, MySQL, MongoDB |

**Pros**:
- Zero configuration — no database server to run
- Self-contained: the entire database is one file
- Perfect for single-user, local-first design
- Built into Python standard library (no extra dependency)

**Cons**:
- Not suitable for concurrent writes from multiple users (not a concern here)
- No built-in migration tooling (handled by lazy init on startup)

---

## Frontend

### Framework: Next.js (TypeScript) — static export mode

| | |
|---|---|
| **Chosen** | Next.js 14+ with `output: 'export'` |
| **Alternatives considered** | Vite + React, SvelteKit, plain HTML/JS |

**Pros**:
- Static export produces plain HTML/CSS/JS — served directly by FastAPI, no Node process at runtime
- Single origin: no CORS issues, one port, one container (future)
- TypeScript provides type safety across the codebase
- Large ecosystem, familiar to most frontend developers

**Cons**:
- Static export mode disables some Next.js features (server components, API routes, image optimization)
- Build step required before serving (not hot-reload in production mode)

---

### Styling: Tailwind CSS

| | |
|---|---|
| **Chosen** | Tailwind CSS with custom dark theme |
| **Alternatives considered** | CSS Modules, Styled Components, Chakra UI |

**Pros**:
- Utility-first approach suits dense, data-rich terminal UIs
- No context-switching between JS and CSS files
- Highly customizable design tokens for the color scheme

**Cons**:
- Long class lists can reduce readability in JSX
- Requires PostCSS setup (included by default in Next.js)

---

### Charting: TBD (Lightweight Charts or Recharts)

| | |
|---|---|
| **Chosen** | Not yet decided — either TradingView Lightweight Charts or Recharts |
| **Decision point** | Frontend implementation phase |

**Lightweight Charts**:
- Canvas-based, extremely performant for financial time-series
- Built by TradingView, designed exactly for this use case
- Smaller bundle, faster rendering for thousands of data points
- Less React-friendly (imperative API)

**Recharts**:
- SVG-based, React-native component API
- Easier to integrate into React state model
- Slower for large datasets but sufficient for session-length sparklines

**Current lean**: Lightweight Charts for the main chart and sparklines; Recharts as fallback if integration proves complex.

---

## Real-time Data Transport: SSE (Server-Sent Events)

| | |
|---|---|
| **Chosen** | SSE via `EventSource` (native browser API) |
| **Alternatives considered** | WebSockets, polling, WebRTC data channels |

**Pros**:
- One-way push is all we need — prices flow server → client only
- Native browser support, no library needed on the client
- Automatic reconnection built into `EventSource`
- Simpler server implementation than WebSockets
- Works through HTTP/1.1 proxies and load balancers

**Cons**:
- One-way only (not suitable if client needs to push data in real time — trades go via REST instead)
- Maximum 6 concurrent connections per browser per domain (not a concern for single-user)

---

## LLM Integration: LiteLLM → OpenRouter → Cerebras

| | |
|---|---|
| **Chosen** | LiteLLM library → OpenRouter API → Cerebras inference provider |
| **Model** | `openrouter/openai/gpt-oss-120b` |
| **Alternatives considered** | Direct Anthropic API, direct OpenAI API, Ollama (local) |

**Pros**:
- Cerebras offers very fast inference (low latency chat responses without streaming)
- OpenRouter provides model routing and fallback
- LiteLLM abstracts the provider — swapping models requires one config change
- Structured outputs (JSON mode) supported

**Cons**:
- Requires `OPENROUTER_API_KEY` — not zero-config
- Cerebras availability depends on OpenRouter routing
- Non-streaming response means a loading indicator is required in the UI

---

## Deployment: Local → Docker (future)

### Current (local development)

**No Docker** — direct process execution:
- Prerequisites: Node.js + npm, Python 3.12+, uv
- Frontend built with `npm run build` → `frontend/out/`
- Backend started with `uv run uvicorn app.main:app --port 8000`
- FastAPI serves static files via `STATIC_FILES_DIR` env var

### Future (Linux server / cloud)

**Docker** — single container:
- Multi-stage Dockerfile: Node stage builds frontend, Python stage runs backend
- `STATIC_FILES_DIR=/app/static/` (set in Dockerfile)
- SQLite persisted via Docker named volume on `/app/db`
- Target platforms: AWS App Runner, Render, or any OCI-compatible host

**Migration effort**: None — `STATIC_FILES_DIR` env var is the only difference between local and Docker runs.

---

_Update this file whenever a technology decision changes or a new one is made._
