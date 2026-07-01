# Hermes Agent Skill Authoring Guide

## Overview

Two places a SKILL.md can live:
1. **User-local:** `~/.hermes/skills/<category>/<name>/SKILL.md` — personal, created via `skill_manage(action='create')`.
2. **In-repo:** `skills/<category>/<name>/SKILL.md` in the hermes-agent repo — committed, shipped with the package.

## Required Frontmatter

Source of truth: `tools/skill_manager_tool.py::_validate_frontmatter`.

```yaml
---
name: my-skill-name               # lowercase, hyphens, ≤64 chars
description: Use when <trigger>. <one-line behavior>.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [short, descriptive, tags]
    related_skills: [other-skill, another-skill]
---
```

Hard requirements: starts with `---` (byte 0, no leading blank line), closes with `\n---\n`, `name` present, `description` ≤ 1024 chars, non-empty body.

## Size Limits

- Description: ≤ 1024 chars
- Full SKILL.md: ≤ 100,000 chars
- Target 8-14k chars; past 20k, split into `references/*.md`

## Peer-Matched Structure

```
# <Title>

## Overview
One or two paragraphs: what and why.

## When to Use
- Bulleted triggers, counter-triggers

## <Topic sections>

## Common Pitfalls

## Verification Checklist
```

## Directory Placement

```
skills/<category>/<name>/SKILL.md
```

Categories: `autonomous-ai-agents`, `creative`, `data-science`, `devops`, `github`, `media`, `mlops/*`, `productivity`, `research`, `security`, `software-development`, and others. Pick the closest existing category.

## Workflow

1. Survey peers in the target category
2. Draft with `write_file` to `skills/<category>/<name>/SKILL.md`
3. Validate locally:
   ```python
   import yaml, re, pathlib
   content = pathlib.Path("skills/<category>/<name>/SKILL.md").read_text()
   assert content.startswith("---")
   m = re.search(r'\n---\s*\n', content[3:])
   fm = yaml.safe_load(content[3:m.start()+3])
   assert "name" in fm and "description" in fm
   assert len(fm["description"]) <= 1024
   ```
4. Git add + commit on the active branch

## Common Pitfalls

1. Using `skill_manage(action='create')` for in-repo skills — writes to `~/.hermes/skills/`, not the repo. Use `write_file` instead.
2. Leading whitespace before `---` — fails `content.startswith("---")` check.
3. Description too generic — start with "Use when ..."
4. Expecting current session to see the new skill — skill loader is cached; verify in a fresh session.
