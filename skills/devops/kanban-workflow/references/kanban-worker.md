# Kanban Worker — Full Reference

Full reference for the worker role, formerly at `skills/devops/kanban-worker/`.

> The **lifecycle** (6 steps: orient → work → heartbeat → block/complete) is auto-injected into every worker's system prompt as `KANBAN_GUIDANCE`. This reference provides deeper detail.

## Workspace handling

| Kind | What it is | How to work |
|------|------------|-------------|
| `scratch` | Fresh tmp dir, yours alone | Read/write freely; GC'd when task is archived |
| `dir:<path>` | Shared persistent directory | Treat like long-lived state |
| `worktree` | Git worktree | If `.git` doesn't exist, run `git worktree add` first |

## Tenant isolation

If `$HERMES_TENANT` is set, prefix memory entries with the tenant.

## Good summary + metadata shapes

**Coding task:** Include changed_files, tests_run/passed, decisions.
**Research task:** Include sources_read, recommendation, benchmarks.
**Review task:** Include pr_number, findings (severity, file, issue).
**Code task needing review:** Use `kanban_block(reason="review-required: ...")` instead of `kanban_complete`.

## Claiming cards

Pass `created_cards=[id1, id2]` on `kanban_complete`. The kernel verifies each id exists and was created by your profile — phantom ids block completion.

## Block reasons that get answered fast

Good: one sentence naming the specific decision needed.
Bad: `"stuck"` — no context.

## Heartbeats

Good: `"epoch 12/50, loss 0.31"`, `"scanned 1.2M/2.4M rows"`.
Bad: `"still working"`, empty notes, sub-second intervals.

## Retry scenarios

- `timed_out` — previous attempt hit `max_runtime_seconds`
- `crashed` — OOM or segfault
- `spawn_failed` — profile config issue, ask human
- `reclaimed` — operator archived the task
- `blocked` — previous attempt blocked; check the unblock comment

## Do NOT

- Call `delegate_task` as a substitute for `kanban_create`
- Modify files outside `$HERMES_KANBAN_WORKSPACE`
- Create follow-up tasks assigned to yourself
- Complete a task you didn't actually finish

## Pitfalls

- Task state can change between dispatch and startup — always `kanban_show` first
- Workspace may have stale artifacts from previous runs
- Don't rely on the CLI when the `kanban_*` tools are available (CLI may not exist in containerized backends)
