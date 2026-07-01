# Consolidation Example — June 2026 Session

This reference documents the actual consolidation performed during one session, as a concrete example of the optimization workflow described in `references/token-efficiency.md`.

## Before (User Profile)

15 entries, 1,288/1,375 chars (93% full):

| Problem | Entries |
|---------|---------|
| Duplicate: same "concise reply" preference twice | #2 and #3 |
| Duplicate: same "approve always" policy twice | #8 and #10 |
| Redundant: "no registration" mentioned in #1 and #9 | #1 and #9 |
| Stale: WeChat account_id in profile (already in memory) | #1 |

## After (User Profile)

12 entries, 1,230/1,375 chars (89% full):

| Change | Saved |
|--------|-------|
| Merged entries 2+3 (concise reply) | ~200 chars |
| Removed entry 9 (don't register, already merged into entry 1) | ~120 chars |
| Removed entry 10 (approve always, already in entry 7) | ~160 chars |
| Added new preference about optimization rationale | +~200 chars |

## Strategy Used

1. **Identify duplicates first** — read all entries, mark pairs that say the same thing
2. **Merge verbosely** — keep the richer version, delete the other
3. **Remove stale** — info that moved to memory or is no longer relevant
4. **Compact** — shorten without losing meaning ("用户说" → just state the fact)

## Key Principle

The user pushed back when skills deletion was proposed ("技能不需要删吧"). The lesson: always explain the token cost model before suggesting what to change. If you say "skills idle cost nothing, memory and profile are the real cost", the user can make informed decisions.
