# Node.js Inspect Debugger — Tool Reference

Two tools, pick one:

- **`node inspect`** — built-in, zero install, CLI REPL. Best for quick poking.
- **`ndb` / CDP via `chrome-remote-interface`** — scriptable from Node/Python; best when you want to automate many breakpoints, collect state across runs, or debug non-interactively from an agent loop.

## Quick Start — `node inspect`

```bash
# Launch a script under the debugger
node inspect myscript.js

# Or attach to an already-running process
node -e "setTimeout(() => {}, 3600000)" &
node inspect -p $PID
```

At the debug REPL:
- `n` — next (step over)
- `s` — step into
- `o` — step out
- `c` — continue
- `repl` — enter interactive mode to evaluate expressions
- `sb('file.js', 42)` — set breakpoint
- `watch('expr')` — add expression to watch list
- `list(n)` — show n lines of source around current line
- `exec expr` — evaluate expression

## Scriptable Debugging (CDP)

For automated debugging from an agent loop:

```javascript
// Using chrome-remote-interface
const CDP = require('chrome-remote-interface');
const client = await CDP({host: '127.0.0.1', port: 9229});
await client.Debugger.enable();
await client.Debugger.pause();
client.on('Debugger.paused', async () => {
    const { callFrames } = await client.Debugger.getStackTrace({stackTraceId: null});
    // Inspect frames
    await client.Debugger.resume();
});
```

## When to Use

- A Node test fails and you need to see intermediate state
- ui-tui crashes or behaves wrong and you want to inspect React/Ink state pre-render
- You need to debug a long-running Node process without restarting it
- Stack traces aren't enough — you need to explore heap/scope values

## Rules

1. Prefer `node inspect` first — it's always available and the REPL is fast.
2. Use CDP scripting when you need break-uninspect-collect automation.
3. Remove breakpoints and debugger statements before committing.
