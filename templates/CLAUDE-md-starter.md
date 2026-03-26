# CLAUDE.md Starter Template

> Fork this and adapt it to your project. See [Chapter 2](../chapters/ch02-claude-md-design.md) for the design principles behind each section.

## Project Context

```markdown
# CLAUDE.md — [Your Project Name]

[One-line description of what this project is.]

## Folder Structure

| Folder | Purpose |
|--------|---------|
| `src/` | ... |
| `docs/` | ... |

## Key Files

| File | Path |
|------|------|
| Config | `...` |
| Entry point | `...` |

## Rules

- [Rule 1: What your agent should always do]
- [Rule 2: What your agent should never do]
- [Rule 3: When to ask for confirmation]

## Tool Preferences

| Task | Preferred Tool | Fallback |
|------|---------------|----------|
| ... | ... | ... |
```

## Design Tips

1. **Start minimal** — 10 lines beats 200 lines on day one.
2. **Add rules from failures** — Every "don't do X" should come from X actually happening.
3. **Separate what from how** — What the agent should achieve vs how it should work.
4. **Version it** — Your CLAUDE.md will evolve. That evolution *is* the learning.
