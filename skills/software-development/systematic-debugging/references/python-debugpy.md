# Python Debugger (pdb + debugpy) — Tool Reference

Three tools, picked by situation:

| Tool | When |
|------|------|
| **`breakpoint()` + pdb** | Local, interactive, simplest. Add `breakpoint()` in the source, run normally, get a REPL at that line. |
| **`python -m pdb`** | Launch an existing script under pdb with no source edits. Useful for quick poking. |
| **`debugpy`** | Remote / headless / "attach to already-running process." Talks DAP, scriptable from terminal, works for long-lived processes (gateway, daemon, PTY children). |

## Quick Start — `breakpoint()`

1. Add `breakpoint()` at the line you want to inspect
2. Run your script normally
3. At the `(Pdb+)` prompt, explore:
   - `locals()` — all local variables
   - `p expr` — print any expression
   - `u` / `d` — move up/down the call stack
   - `l` — show surrounding source code
   - `c` — continue execution
   - `q` — quit

## Advanced pdb

```
breakpoint()  # Built-in since Python 3.7
```

At the REPL: `locals()`, `p some_var`, `u`/`d` (stack), `l` (source), `c` (continue), `q` (quit).

## debugpy (Remote/Headless)

```
pip install debugpy
python -m debugpy --listen 5678 --wait-for-client myscript.py
```

Then attach from VS Code, or use the terminal to script interaction.

## When to Use

- A test fails and the traceback doesn't reveal why a value is wrong
- You need to inspect intermediate state across multiple function calls
- A bug only manifests 50% of the time — add breakpoint and check conditions
- The error is in a long-running process (gateway, daemon) — use debugpy
- You want to explore an object's structure interactively

## Rules

1. Start with `breakpoint()` — it's the cheapest thing that works.
2. Only move to `debugpy` if you need remote/headless debugging.
3. Always remove `breakpoint()` calls from production code.
