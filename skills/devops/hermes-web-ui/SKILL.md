---
name: hermes-web-ui
category: devops
tags: [hermes, web-ui, admin, troubleshooting, login]
triggers:
  - "hermes-web-ui 登录不了"
  - "web UI 登不上"
  - "账号密码不对"
  - "login failed hermes-web-ui"
  - "web ui 密码忘记"
  - "hermes-web-ui start/stop/restart"
  - "update hermes-web-ui"
description: Administer and troubleshoot Hermes Web UI (npm package, port 8648). Covers start/stop, login credential reset, login lock handling, port config, and update.

---

# Hermes Web UI Administration

## Overview

Hermes Web UI (`hermes-web-ui`) is an npm global package providing a web dashboard for Hermes Agent. Runs on port 8648 by default.

```
npm install -g hermes-web-ui
hermes-web-ui start [port]
```

## Quick Reference

| Item | Value / Path |
|------|-------------|
| Default username | `admin` |
| Default password | `123456` |
| Data directory | `~/.hermes-web-ui/` |
| SQLite DB | `~/.hermes-web-ui/hermes-web-ui.db` |
| Server log | `~/.hermes-web-ui/logs/server.log` |
| Login lock file | `~/.hermes-web-ui/.login-lock.json` |
| Auth token file | `~/.hermes-web-ui/.token` |
| Port (default) | 8648 |
| Current PID | `~/.hermes-web-ui/server.pid` |

## Login Troubleshooting

### Step 1 — Verify the server is running

```bash
ps aux | grep hermes-web-ui
# Should show: node .../hermes-web-ui/dist/server/index.js
```

If not running: `hermes-web-ui start`

### Step 2 — Reset the default login

If you forgot the password or it was scrambled:

```bash
hermes-web-ui reset-default-login
# Output: ✓ Reset default login: admin / 123456
```

This re-hashes the default password and writes it into the SQLite DB `users` table.

### Step 3 — Check login lock file

After too many failed attempts, the server may rate-limit your IP.

Check `~/.hermes-web-ui/.login-lock.json`:

```json
{
  "passwordIpMap": {
    "192.168.1.x": {
      "failures": 6,
      "lockedUntil": 0,
      "firstFailureAt": 1700000000000
    }
  },
  "globalTotalFailures": 6,
  "globalLockedUntil": 0
}
```

**Lock thresholds (from source):**
- 10 failed password attempts from same IP → IP locked for 15 minutes
- 50 failed attempts globally → all IPs locked for 30 minutes

**Clear the lock:**
```bash
# Clears .login-lock.json file
hermes-web-ui clear-login-locks

# If server is running, restart to clear in-memory locks too:
hermes-web-ui restart
```

Or manually delete the file:
```bash
rm ~/.hermes-web-ui/.login-lock.json
```

### Step 4 — Check AUTH_TOKEN override

If `AUTH_TOKEN` env var is set, the server uses it instead of the token file. Check:
```bash
echo $AUTH_TOKEN
grep AUTH_TOKEN ~/.hermes/.env
grep AUTH_TOKEN ~/.env
```

### Step 5 — Check the server log

```bash
tail -50 ~/.hermes-web-ui/logs/server.log
```

Common log patterns:
- `Update available: 0.6.5 → 0.6.14` — a newer version exists, but this is informational, not a failure.
- Failed login attempts are logged.

## Commands

| Command | Description |
|---------|-------------|
| `hermes-web-ui start [port]` | Start the server (default port 8648) |
| `hermes-web-ui restart [port]` | Restart the server |
| `hermes-web-ui stop` | Stop the server |
| `hermes-web-ui reset-default-login` | Reset admin/123456 credentials |
| `hermes-web-ui clear-login-locks` | Delete login IP lock file |
| `hermes-web-ui update` | Update to latest version and restart |
| `hermes-web-ui --port <port>` | Specify port (with start/restart) |

## Pitfalls

- **Log file floods with "Update available"** — harmless. The server checks for updates every tick. To suppress, update to latest with `hermes-web-ui update`.
- **Clearing login lock file is not enough when server is running** — the lock is cached in memory. Use `hermes-web-ui restart` after `clear-login-locks`.
- **`ss -tlnp` shows no listening port** — Termux limitations. Use `ps aux | grep hermes-web-ui` to verify the process is running, then check the PID file at `~/.hermes-web-ui/server.pid`.
- **Node.js version compatibility** — Ensure Node.js is recent enough. Check with `node --version` (v18+ recommended).
- **After npm update, process may still run old version** — use `hermes-web-ui restart` to pick up the updated binary.

## Update

```bash
# Check current version
npm list -g hermes-web-ui

# Update
npm update -g hermes-web-ui
hermes-web-ui update  # auto restart after update
```

## Files Layout

```
~/.hermes-web-ui/
├── .token              # Auto-generated 64-char hex auth token
├── .login-lock.json    # IP login rate-limit state
├── hermes-web-ui.db    # SQLite DB (users, sessions)
├── hermes-web-ui.db-shm
├── hermes-web-ui.db-wal
├── server.pid          # PID of running server
├── server.log          # stdout/stderr
├── logs/               # Rotated logs
└── upload/             # File uploads
```
