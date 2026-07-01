---
name: kanban-workflow
description: "Hermes Kanban workflow — orchestrator decomposition playbook AND worker pitfalls/examples. Load when acting as an orchestrator routing work or a worker handling a task."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows, android]
metadata:
  hermes:
    tags: [kanban, multi-agent, orchestration, routing, worker, workflow]
    related_skills: []
---

# Kanban Workflow

The Hermes Kanban system has two roles — **orchestrators** (who decompose goals into tasks) and **workers** (who execute individual tasks). The core lifecycle (the `kanban_create` fan-out pattern, the "decompose, don't execute" rule, the 6-step worker lifecycle) is auto-injected into every process via `KANBAN_GUIDANCE`. This skill provides deeper detail for both roles.

## Orchestrator Role — `references/kanban-orchestrator.md`

**Load this when you're acting as a planner/orchestrator profile** whose job is to decompose a user goal into Kanban tasks and assign them to the right specialist profiles.

Key rules:
1. **Discover available profiles** before planning — `hermes profile list` or ask the user
2. **Decompose, don't execute** — your restricted toolset isn't for implementation
3. **Extract lanes** from the user prompt, create one card per independent workstream
4. **Link dependencies** with `parents=[...]` so the dispatcher respects ordering
5. **Run independent lanes in parallel** — link only true data dependencies
6. **Report back** with a clear summary of the task graph you created

## Worker Role — `references/kanban-worker.md`

**Load this when you're spawned as a Kanban worker** (it's loaded automatically for every dispatched worker via `--skills kanban-worker`). Provides good handoff shapes, retry diagnostics, edge cases, and workspace handling.

Key patterns:
1. **`kanban_show` first** — task state may have changed between dispatch and startup
2. **Good summary + metadata** — structured handoffs for downstream workers
3. **`review-required` blocking** — for code tasks that need human eyes before merging
4. **Heartbeats worth sending** — progress, not "still working"
5. **Retry diagnostics** — read prior run outcomes to avoid repeating failures
6. **`created_cards` verification** — only pass IDs from successful `kanban_create` returns

## When to use Kanban vs delegate_task

Use Kanban when:
- Multiple specialists are needed
- The work should survive a crash or restart
- The user might want to interject (human-in-the-loop)
- Multiple subtasks can run in parallel
- Review/iteration is expected
- The audit trail matters

Use `delegate_task` for short, bounded reasoning subtasks inside a single run.
