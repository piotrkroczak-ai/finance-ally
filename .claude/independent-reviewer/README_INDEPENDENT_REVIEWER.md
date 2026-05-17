# Independent Reviewer Plugin

Auto-log your git changes with Claude Code - Zero manual work.

A Claude Code plugin that automatically tracks and documents code changes in your project's `planning/logs.md` file after each session.

---

## Features

- Automatic Logging: Captures git changes at the end of each Claude Code session
- Smart Formatting: Generates structured log entries with timestamps
- Selective: Only logs when actual changes were made (ignores pure conversation sessions)
- Non-Intrusive: Runs silently in the background, won't clutter your workflow
- PowerShell Ready: Optimized for Windows development environments
- Git Integration: Uses `git status` and `git diff` for accurate change detection

---

## Installation

### 1. Clone or Download

```bash
# In your Claude project root
git clone https://github.com/piotrkroczak-ai/independent-reviewer .claude/independent-reviewer
```

Or manually copy the plugin folder to `.claude/independent-reviewer/`.

### 2. Enable in Claude Code

The plugin auto-discovers. If not showing:
- Restart Claude Code
- Check: Plugins tab should show "independent-reviewer"

### 3. Create planning/logs.md (First Time Only)

```bash
mkdir -p planning
touch planning/logs.md
```

---

## How It Works

### Workflow

```
Start Claude Code Session
    |
    v
Make code changes (or just chat)
    |
    v
Exit/Stop Claude Code
    |
    v
Plugin runs automatically:
  - Detects git changes (git status, git diff)
  - If changes found: append entry to planning/logs.md
  - If no changes: do nothing silently
    |
    v
Continue next session
```

### Log Entry Format

When changes are detected, the plugin appends:

```markdown
## YYYY-MM-DD HH:MM - Brief title

- what changed or was implemented
- any decision made or open question resolved
```

Example:

```markdown
## 2025-05-20 14:32 - Add agent orchestration docs

- Created agent-architecture.md with orchestration patterns
- Documented context budgeting best practices
- Resolved: how to handle token overflow in multi-agent systems
```

---

## Requirements

- Claude Code v2.1.0+ (comes with latest Claude)
- Git repository initialized in your project
- PowerShell (Windows) or Bash (macOS/Linux)
- planning/logs.md file in your project root (auto-created on first run)

---

## Configuration

### Default Behavior

The plugin works out-of-the-box with sensible defaults:
- Event: Triggered on Stop (when Claude Code exits)
- Shell: PowerShell (Windows)
- Timeout: 120 seconds
- Async: True (non-blocking)

### Customize (Optional)

Edit `.claude/independent-reviewer/manifest.json`:

```json
{
  "hooks": [
    {
      "event": "Stop",
      "command": "codex exec 'Your custom command here'",
      "shell": "powershell",
      "timeout": 120,
      "async": true,
      "statusMessage": "Updating logs.md..."
    }
  ]
}
```

---

## Usage Examples

### Example 1: Typical Session

What you do:

```bash
claude
# Work on features for 30 minutes
# Exit Claude Code
```

Result in `planning/logs.md`:

```markdown
## 2025-05-20 10:45 - Implement agent loop error handling

- Added retry logic for tool execution failures
- Implemented escalation mechanism for critical errors
- Decided: use exponential backoff for retries (max 3 attempts)
```

### Example 2: Pure Conversation (No Logging)

What you do:

```bash
claude
# Ask Claude questions about architecture (no code changes)
# Exit Claude Code
```

Result:
- Nothing logged (no changes detected)
- `planning/logs.md` stays unchanged

### Example 3: Multiple Changes

What you do:

```bash
claude
# Create file: src/agents.py
# Modify: src/config.py
# Delete: legacy/old_agent.py
# Exit Claude Code
```

Result in `planning/logs.md`:

```markdown
## 2025-05-20 15:22 - Refactor agent initialization

- Created agents.py with coordinator pattern
- Updated config.py with new agent schemas
- Removed legacy agent implementation
- Resolved: centralized vs distributed orchestration (chose coordinator)
```

---

## What Gets Logged

### Detected and Logged

- New files created
- Files modified (including documentation)
- Files deleted
- Directory structure changes

### NOT Logged

- Pure conversation (no file changes)
- Git status checks only
- File reads without modifications

---

## Troubleshooting

### "Updating logs.md..." but nothing happens

Cause: `planning/logs.md` doesn't exist

Fix:

```bash
mkdir -p planning
echo "# Project Logs" > planning/logs.md
```

### Plugin not running on exit

Cause: Plugin not installed or disabled

Fix:
1. Check Claude Code plugins: Plugins tab
2. Look for "independent-reviewer" - should be enabled
3. If missing: restart Claude Code

### Git command not found

Cause: Git not in PATH

Fix:

```bash
# Check if git is installed
git --version

# If not: install Git from https://git-scm.com/
```

### Permission denied (PowerShell)

Cause: Execution policy restricted

Fix:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

## Log Entry Best Practices

### Write Clear Summaries

Bad:

```markdown
## 2025-05-20 10:00 - stuff

- did things
- changed code
```

Good:

```markdown
## 2025-05-20 10:00 - Implement context budgeting for multi-agent system

- Added token allocation strategy for 5 concurrent agents
- Implemented early warning at 80% utilization
- Documented escalation rules in ADR-007
- Resolved: dynamic vs static token budgeting (chose dynamic)
```

### Include Decisions

Always note key decisions made during the session:

```markdown
- Decided: use uv instead of pip for dependency management
- Resolved: agent timeout = 30s (was debating 15s vs 45s)
- Chose: coordinator pattern over autonomous agents for this use case
```

### Link to Files

If relevant:

```markdown
- Created: src/agents/coordinator.py (see for implementation details)
- Updated: docs/architecture.md (see for design decisions)
- Removed: legacy/v1_agents.py (full migration to v2 complete)
```

---

## Workflow Integration

### With Git Commits

Pair the plugin with your commit workflow:

```bash
# Session 1: Code + auto-log
claude
# ... make changes ...
# Exit --> auto-logged to planning/logs.md

# Session 2: Review logs, then commit
git add .
git commit -m "Feature: agent orchestration - see planning/logs.md for details"
```

### With Project Planning

Keep `planning/logs.md` as your session journal:

```
planning/
├── PLAN.md              (high-level roadmap)
├── logs.md              (session-by-session record)
└── REVIEW.md            (peer feedback)
```

---

## Tips and Tricks

### Tip 1: Start Each Session Fresh

Make sure you have a clear task before starting:

```bash
# Good: Clear goal
claude
# "Implement token budgeting for agents"

# Vague: Unclear scope
claude
# "Work on stuff"
```

### Tip 2: Review Logs Weekly

Set a reminder to review `planning/logs.md`:
- See what you accomplished
- Identify patterns (e.g., always hit token limits?)
- Plan next week based on what you learned

### Tip 3: Use Logs for Documentation

Your logs become documentation:
- Architectural decisions
- What was tried and rejected
- Why certain patterns were chosen

Example: Turn logs into ADR (Architecture Decision Records):

```bash
planning/logs.md -> planning/adr/ADR-001-agent-orchestration.md
```

---

## Contributing

Found a bug? Have a suggestion?

1. Report issues: https://github.com/piotrkroczak-ai/independent-reviewer/issues
2. Suggestions: Open a discussion or PR
3. Improvements: Fork and submit PRs welcome

---

## License

MIT License - Use freely in personal and commercial projects.

---

## Author

Piotr Kroczak
- GitHub: https://github.com/piotrkroczak-ai
- LinkedIn: https://www.linkedin.com/in/piotrkroczak/

---

## Quick Start Checklist

- Install plugin (copy to `.claude/independent-reviewer/`)
- Create `planning/logs.md` (run: `mkdir -p planning && touch planning/logs.md`)
- Restart Claude Code
- Check Plugins tab - should see "independent-reviewer"
- Make some code changes
- Exit Claude Code
- Check `planning/logs.md` - should have new entry

---

## Support

### Frequently Asked Questions

Q: Does it work with GitHub/GitLab CI/CD?
A: Yes. It just reads git status - works anywhere git works.

Q: Can I customize the log format?
A: Yes - edit the command in `manifest.json` to match your preferred format.

Q: Does it track private repos only?
A: No - works with any git repo (public, private, local).

Q: Can I disable it temporarily?
A: Yes - disable in Plugins tab, or set "async": false to make it optional.

---

## Next Steps

- Read the installation guide above
- Try a usage example
- Check the troubleshooting section if stuck
- Join our community: https://github.com/piotrkroczak-ai/independent-reviewer/discussions

---

Happy logging.

Last updated: May 2025 | Version: 1.0.0
