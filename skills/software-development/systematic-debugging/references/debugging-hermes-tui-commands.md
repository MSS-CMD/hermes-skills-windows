# Debugging Hermes TUI Slash Commands — Tool Reference

Hermes slash commands span three layers — Python command registry, tui_gateway JSON-RPC bridge, and the Ink/TypeScript frontend. When a command misbehaves (missing from autocomplete, works in CLI but not TUI, config persists but UI doesn't update), the bug is almost always one layer being out of sync with another.

## Architecture

```
Python backend (hermes_cli/commands.py)     <- canonical COMMAND_REGISTRY
       │
       ▼
TUI gateway (tui_gateway/server.py)         <- slash.exec / command.dispatch
       │
       ▼
TUI frontend (ui-tui/src/app/slash/)        <- local handlers + fallthrough
```

Command definitions must be registered consistently across Python and TypeScript.

## Investigation Steps

1. **Check if the command exists in the TUI frontend:**
   ```bash
   search_files --pattern "/commandname" --file_glob "*.ts" --path ui-tui/
   search_files --pattern "/commandname" --file_glob "*.tsx" --path ui-tui/
   ```

2. **Check the Python backend registry:**
   ```bash
   search_files --pattern '"commandname"' --file_glob "*.py" --path hermes_cli/
   ```

3. **Check the tui_gateway bridge:**
   ```bash
   search_files --pattern "commandname" --file_glob "*.py" --path tui_gateway/
   ```

4. **Check frontend command metadata:**
   ```bash
   search_files --pattern "all_commands" --file_glob "*.ts" --path ui-tui/src/
   ```

## Common Issues

- Command registered in Python but NOT in TypeScript → autocomplete missing
- Command registered in TUI but NOT in Python → CLI works, TUI doesn't
- Config-change commands need `hermes_custom_bridge` to notify TUI
- TypeScript uses the `COMMAND_REGISTRY` for autocomplete, help texts, and routing
