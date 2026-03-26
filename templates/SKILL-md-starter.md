# SKILL.md Starter Template

> A skill is a reusable instruction set that your agent executes when triggered. See [Chapter 3](../chapters/ch03-skill-architecture.md) for the architecture behind good skills.

```markdown
---
name: [skill-name]
description: [One-line description — used for discovery and routing]
---

# /[skill-name] — [Human-readable title]

[Brief description of what this skill does and when to use it.]

## Trigger

- User says: "[natural language trigger]"
- Or: `/[skill-name]` command

## Steps

### Step 1: [Action name]

[What to do, in imperative form.]

### Step 2: [Action name]

[Next step.]

## Output Format

```
[Expected output template]
```

## Guard Rails

- [What this skill should NOT do]
- [When to stop and ask the user]
```

## Design Tips

1. **Steps are imperative** — "Read the file" not "The file should be read."
2. **Include guard rails** — What the skill should refuse to do matters as much as what it does.
3. **Define the output** — If you don't specify format, you'll get a different one every time.
4. **Test with edge cases** — A skill that works on happy paths but breaks on edge cases will erode trust.
