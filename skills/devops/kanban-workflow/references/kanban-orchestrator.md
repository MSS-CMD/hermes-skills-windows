# Kanban Orchestrator — Full Reference

Full reference for the orchestrator role, formerly at `skills/devops/kanban-orchestrator/`.

> Note: The **core worker lifecycle** is auto-injected into every kanban process via the `KANBAN_GUIDANCE` system-prompt block. This reference is the deeper playbook for the orchestrator role.

## Profiles are user-configured

Before fanning out, discover available profiles:
- `hermes profile list`
- `kanban_list(assignee="<name>")` — sanity-check a single name
- **Just ask the user**

## When to use the board

Create Kanban tasks when:
1. Multiple specialists are needed
2. The work should survive a crash or restart
3. The user might want to interject (human-in-the-loop)
4. Multiple subtasks can run in parallel
5. Review/iteration is expected
6. The audit trail matters

If none apply, use `delegate_task` instead.

## The anti-temptation rules

- **Do not execute the work yourself** — route, don't execute
- **For any concrete task, create a Kanban task and assign it**
- **Split multi-lane requests** before creating cards
- **Run independent lanes in parallel**
- **Use `parents=[...]` for dependencies** — never create cards as independent and link later
- **Don't invent profile names** — the dispatcher silently drops unknown assignees

## Decomposition Playbook

### Step 1 — Understand the goal
### Step 2 — Sketch the task graph
### Step 3 — Create tasks with `kanban_create` and link dependencies
### Step 4 — Complete your own orchestrator task
### Step 5 — Report back to the user

## Common patterns

**Fan-out + fan-in:** N research cards with no parents, one synthesis card with them as parents.
**Parallel implementation + validation:** implementer + researcher in parallel, reviewer depends on both.
**Pipeline with gates:** `planner → implementer → reviewer`.
**Same-profile queue:** N tasks, same profile, no parent links.
**Human-in-the-loop:** Any task can `kanban_block()` to wait for input.

## Pitfalls

- Inventing profile names that don't exist
- Bundling independent lanes into one card
- Over-linking because of wording
- Forgetting dependency links
- Reassignment vs. new task
- Argument order for `kanban_link(parent_id, child_id)` — parent first
- Pre-creating the full graph when intermediate findings determine the shape
