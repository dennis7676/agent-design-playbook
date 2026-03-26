# Worksheet 2: CLAUDE.md Evolution Exercise

> Companion to [Chapter 2 — CLAUDE.md Design](../chapters/ch02-claude-md-design.md)

## Before You Start

This worksheet guides you through building (or auditing) your CLAUDE.md using the three-layer model: Map → Guardrails → Cognition.

---

## Exercise 1: Layer 1 — The Map (15 min)

Fill in the blanks for your project:

### Workspace Structure
```markdown
## Folder Structure
| Folder | Purpose |
|--------|---------|
| [folder1] | [what goes here] |
| [folder2] | [what goes here] |
| [folder3] | [what goes here] |
```

### Tool Roles
```markdown
## Tool Roles
| Tool | Role |
|------|------|
| [tool1] | [primary responsibility] |
| [tool2] | [primary responsibility] |
```

### Key File Locations
```markdown
## Key Files
| File | Path | Purpose |
|------|------|---------|
| [config] | [path] | [what it controls] |
| [data] | [path] | [what it stores] |
```

**Checkpoint**: Can a brand-new agent read this and know where everything is? If not, what's missing?

---

## Exercise 2: Layer 2 — The Guardrails (15 min)

### Your Risk Classification

Think about the actions your agent takes. Classify each into risk levels:

| Action | Reversible in 5 min? | Affects outside workspace? | Risk Level |
|--------|----------------------|---------------------------|------------|
| [action1] | Yes / No | Yes / No | L__ |
| [action2] | Yes / No | Yes / No | L__ |
| [action3] | Yes / No | Yes / No | L__ |
| [action4] | Yes / No | Yes / No | L__ |

**Rule of thumb**:
- Both "Yes" + "No" → L1-L2 (autonomous)
- Either triggers → L3-L4 (confirm first)
- Both "No" + "Yes" → L5 (human required)

### Your Failure Log

List 3 mistakes your agent has made (or could make):

| Mistake | What happened | What rule would prevent it |
|---------|--------------|--------------------------|
| 1. | | |
| 2. | | |
| 3. | | |

**Each failure becomes a guardrail.** Write it as a rule for your CLAUDE.md.

---

## Exercise 3: Layer 3 — The Cognition (20 min)

### Ambiguity Scenarios

List 3 ambiguous inputs your agent might receive:

| Input | Possible interpretations | How should the agent decide? |
|-------|------------------------|------------------------------|
| "[vague request 1]" | A: __ / B: __ | |
| "[vague request 2]" | A: __ / B: __ | |
| "[vague request 3]" | A: __ / B: __ | |

For each: should the agent guess, ask, or use a pattern? At what confidence threshold?

### Decision Patterns

Think of 3 recurring decisions your agent makes:

| Decision type | Your usual choice | Why? |
|--------------|------------------|------|
| [e.g., "tool selection"] | [e.g., "try lightweight first"] | [e.g., "avoid over-engineering"] |
| | | |
| | | |

These patterns can become documented rules in your CLAUDE.md.

---

## Discussion Prompts (Study Group)

1. **"What's the most important guardrail in your system?"** The one that, if removed, would cause the biggest failure.

2. **"How do you handle ambiguity today?"** Does your agent ask, guess, or stall? Which response is most useful?

3. **Share your failure log.** What patterns emerge across the group? Are there universal guardrails?

---

## Deliverable

By the end of this worksheet, you should have a CLAUDE.md draft with:
- [ ] Layer 1: Folder structure + tool roles + key files
- [ ] Layer 2: At least 3 guardrail rules, each traceable to a failure
- [ ] Layer 3: At least 1 ambiguity resolution pattern

Use the [CLAUDE.md Starter Template](../templates/CLAUDE-md-starter.md) as your starting point.
