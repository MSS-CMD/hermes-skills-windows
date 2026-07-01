# OpenCode CLI — Full Reference

Full standalone reference for OpenCode CLI, formerly at `skills/autonomous-ai-agents/opencode/`.

> **When to use OpenCode:** User explicitly asks for OpenCode, or you want a provider-agnostic, open-source external coding agent.

## Prerequisites

- OpenCode installed: `npm i -g opencode-ai@latest` or `brew install anomalyco/tap/opencode`
- Auth configured: `opencode auth login` or set provider env vars (OPENROUTER_API_KEY, etc.)
- Verify: `opencode auth list` should show at least one provider
- Git repository for code tasks (recommended)
- `pty=true` for interactive TUI sessions

## Binary Resolution

Shell environments may resolve different OpenCode binaries. If behavior differs, check:
```
terminal(command="which -a opencode")
terminal(command="opencode --version")
```

Pin explicit binary path if needed:
```
terminal(command="$HOME/.opencode/bin/opencode run '...'", workdir="~/project", pty=true)
```

## One-Shot Tasks

Use `opencode run` for bounded, non-interactive tasks:
```
terminal(command="opencode run 'Add retry logic to API calls and update tests'", workdir="~/project")
```
Attach context files with `-f`:
```
terminal(command="opencode run 'Review this config for security issues' -f config.yaml -f .env.example", workdir="~/project")
```
Show model thinking with `--thinking`:
```
terminal(command="opencode run 'Debug why tests fail in CI' --thinking", workdir="~/project")
```
Force a specific model:
```
terminal(command="opencode run 'Refactor auth module' --model openrouter/anthropic/claude-sonnet-4", workdir="~/project")
```

## Interactive Sessions (Background)

```
terminal(command="opencode", workdir="~/project", background=true, pty=true)
process(action="submit", session_id="<id>", data="Implement OAuth refresh flow and add tests")
process(action="poll", session_id="<id>")
process(action="log", session_id="<id>")
```

**Important:** Do NOT use `/exit` — it is not a valid OpenCode command. Use Ctrl+C (`\x03`) or `process(action="kill")` to exit.

## Common Flags

| Flag | Use |
|------|-----|
| `run 'prompt'` | One-shot execution and exit |
| `--continue` / `-c` | Continue the last OpenCode session |
| `--session <id>` / `-s` | Continue a specific session |
| `--agent <name>` | Choose OpenCode agent (build or plan) |
| `--model provider/model` | Force specific model |
| `--thinking` | Show model thinking blocks |
| `--file <path>` / `-f` | Attach file(s) to the message |
| `--title <name>` | Name the session |

## PR Review

OpenCode has a built-in PR command:
```
terminal(command="opencode pr 42", workdir="~/project", pty=true)
```

## Session & Cost Management

```
terminal(command="opencode session list")
terminal(command="opencode stats")
terminal(command="opencode stats --days 7 --models anthropic/claude-sonnet-4")
```

## Pitfalls

- Interactive `opencode` (TUI) sessions require `pty=true`. The `opencode run` command does NOT need pty.
- `/exit` is NOT a valid command — it opens an agent selector. Use Ctrl+C to exit the TUI.
- PATH mismatch can select the wrong OpenCode binary/model config.
- If OpenCode appears stuck, inspect logs before killing: `process(action="log", session_id="<id>")`
- Avoid sharing one working directory across parallel OpenCode sessions.
- Enter may need to be pressed twice to submit in the TUI.
