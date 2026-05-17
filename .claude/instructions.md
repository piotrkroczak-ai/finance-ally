# Instructions pour Claude

## Mes Préférences de Communication

- Préfère communication en français avec termes techniques en English
- Focus: code production-ready en anglais, projets portfolio, explications progressives
- JAMAIS d'émojis ou d'emoticones dans le contenu sauf si explicitement demandé

---

## VERY IMPORTANT - Mon Approche

- Be simple. Approach tasks in a simple, incremental way.
- Work incrementally ALWAYS. Small, simple steps. Validate and check each increment before moving on.
- Use LATEST apis as of now
- Never overengineer. Do not program defensively. Use exception managers only when needed.
- Identify root cause before fixing issues. Prove with evidence, then fix.
- Try one test at a time. Be methodical.

---

## MANDATORY Code Style

- Do not overengineer. Do not program defensively.
- Identify root cause before fixing issues. Prove with evidence, then fix.
- Work incrementally with small steps. Validate each increment.
- Use latest library APIs.
- Favor clear, concise docstring comments. Be sparing with comments outside docstrings.
- Favor short modules, short methods and functions. Name things clearly.
- Debugging: PROVE THE PROBLEM FIRST - don't guess. Don't jump to conclusions.

---

## Python Environment

- Prefer UV for lightweight projects, automation tools, web apps, and agentic systems
- NEVER mix pip + conda + uv in same environment unless absolutely necessary
- When using UV:
  - Use: uv add xxx (not pip install xxx)
  - Use: uv run xxx (not python xxx)
  - Always commit: uv.lock and pyproject.toml
  - Never commit: .venv/ directory

> For machine-specific setup (paths, drive config), see `.claude/local-setup.md` (gitignored, personal PC only).

---

## Important - Debugging and Fixing

When troubleshooting problems:
- ALWAYS identify root cause BEFORE fixing
- PROVE THE PROBLEM FIRST - don't guess
- Try one test at a time. Be methodical
- Don't jump to conclusions. Don't apply workarounds
- Ask me to verify the problem exists before suggesting fixes

---

## About My AI/ML Focus

When discussing agents or architecture:
- Assume I want production-ready patterns (not learning examples)
- Focus on scalability and real-world constraints
- Explain anti-patterns and why they fail
- Show tradeoffs (simplicity vs robustness)

---

## Summary

**Always:**
- French language + English tech terms
- Production-ready code in English
- No emojis unless asked
- Commit uv.lock and pyproject.toml
- Root cause analysis first
- Simple, incremental steps — validate each one
