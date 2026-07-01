# Hermes Token Efficiency

How to reduce token consumption per turn in Hermes Agent.

## Token Cost Breakdown

| Component | Injected Every Turn? | Token Cost | Optimization Impact |
|-----------|---------------------|------------|-------------------|
| **Memory** (your notes) | ✅ Yes | **High** — up to 3,000 chars | **BIG SAVINGS** |
| **User Profile** | ✅ Yes | **High** — up to 1,375 chars | **BIG SAVINGS** |
| **System prompt** | ✅ Yes | Fixed (~1,500 chars) | Hard to reduce |
| **Installed idle skills** | ❌ No | Negligible | Don't bother deleting |

### Key Insight

**Skills sitting idle cost ~0 tokens per turn.** They're only loaded when explicitly referenced. Deleting them to "save tokens" is counterproductive — you lose capability for no gain.

**Memory + User Profile are the real cost.** Shrinking them by 100 chars saves ~130-150 tokens per turn.

## Optimization Strategy

### Step 1: Audit
Check the MEMORY and USER PROFILE sections in the system prompt for usage percentages.

### Step 2: Consolidate Memory
- Merge related entries (two entries about the same topic → one)
- Remove stale info (old account IDs, deprecated tools)
- Shorten verbose entries
- Move procedural knowledge to skills (not memory)
- **Target:** ~50% usage

### Step 3: Consolidate User Profile
- Merge duplicates ("prefers concise replies" + "keep replies short")
- Remove redundant detail
- Move procedural preferences into the relevant skill's pitfalls section
- **Target:** ~60% usage

## Pitfalls

- **Don't suggest deleting idle skills** to save tokens — they cost nothing sitting there.
- **Memory/profile updates take effect next turn**, not immediately.
- **Don't put step-by-step instructions in memory** — those belong in skills.
- **Don't over-optimize.** 50% usage is fine. Cutting below usually removes useful context.
