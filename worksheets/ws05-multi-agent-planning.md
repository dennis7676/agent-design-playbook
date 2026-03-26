# Worksheet 5: Multi-Agent System Planning

> Companion to [Chapter 5 — Multi-Agent Operations](../chapters/ch05-multi-agent-ops.md)

## Before You Start

Use this worksheet to decide whether you need multiple agents, and if so, how to coordinate them. **Not everyone needs multi-agent** — this worksheet starts with that question.

---

## Exercise 1: Do You Need Multiple Agents? (5 min)

Check all that apply:

- [ ] I need processing to happen when I'm not at my computer (async)
- [ ] My work spans multiple environments (terminal + server + pipeline)
- [ ] A single session can't handle my daily volume
- [ ] Different tasks need different models or tools

**If 0-1 checked**: You probably don't need multi-agent. One well-designed agent is better.
**If 2+ checked**: Multi-agent may be worth the coordination cost. Continue.

---

## Exercise 2: Role Definition (10 min)

Define each agent's role. The key constraint: **route by environment, not capability.**

| Agent | Environment | Availability | Primary role |
|-------|------------|-------------|-------------|
| Agent 1 | [e.g., terminal] | [e.g., on-demand] | |
| Agent 2 | [e.g., server] | [e.g., 24/7] | |
| Agent 3 | [e.g., pipeline] | [e.g., scheduled] | |

**Overlap check**: For each pair of agents, list tasks they could BOTH handle:

| Agents | Overlapping tasks | Who owns it? | Why? |
|--------|------------------|-------------|------|
| 1 & 2 | | | |
| 1 & 3 | | | |
| 2 & 3 | | | |

Every overlapping task must have exactly one owner.

---

## Exercise 3: Design Your Routing Table (10 min)

| Input type | Owner | Fallback | Never |
|-----------|-------|----------|-------|
| [e.g., user message] | | | |
| [e.g., scheduled job] | | | |
| [e.g., webhook event] | | | |
| [e.g., file change] | | | |
| [e.g., error alert] | | | |

**Test**: For each input, is there exactly one path? If an input could go to two agents, the routing table is ambiguous.

---

## Exercise 4: Shared State Design (15 min)

What information do agents need to share?

| Information | Who writes it? | Who reads it? | Format | Location |
|------------|---------------|--------------|--------|----------|
| Task status | | | | |
| Session history | | | | |
| Config/settings | | | | |
| Error logs | | | | |

**Design your shared state**:

1. **Task tracker** (what format?): _______________
2. **Session logs** (what structure?): _______________
3. **Shared constitution** (what file?): _______________

---

## Exercise 5: Write a Handoff Template (10 min)

Design the handoff format your agents will use:

```markdown
## Handoff — [timestamp]
> [one-line summary]

## Completed
- [what's done]

## In Progress
- [what's partially done + current state]

## Next Steps
- [ ] [specific action items for receiving agent]

## Context
- [decisions made and WHY]
- [files touched]
- [blockers]
```

**The test**: Could an agent with zero prior context read this handoff and continue the work correctly?

---

## Exercise 6: Conflict Resolution Protocol (5 min)

When two agents disagree, what happens?

Step 1: _________________________________ (e.g., check ownership)
Step 2: _________________________________ (e.g., score with framework)
Step 3: _________________________________ (e.g., escalate to human)

What percentage of conflicts should reach the human? Target: <10%.

---

## Discussion Prompts (Study Group)

1. **"Who went multi-agent and regretted it?"** What was the coordination cost? Was it worth it?

2. **"What's your worst handoff failure?"** What information was lost? How did you fix the template?

3. **"Single agent vs. multi-agent — convince the other side."** Debate the trade-offs.

---

## Deliverable

By the end, you should have:
- [ ] A clear yes/no on whether you need multi-agent
- [ ] If yes: role table, routing table, shared state design, handoff template
- [ ] If no: a documented reason (valuable for revisiting later)
