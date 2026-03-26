# Chapter 5: Multi-Agent Operations — When One Agent Isn't Enough

> Managing one agent is a design problem. Managing three is an **organizational** problem.

## TL;DR

Running multiple agents sounds like a scaling solution. In practice, it introduces three coordination challenges that don't exist with a single agent: ownership ambiguity, context fragmentation, and handoff failure. This chapter traces how a 3-agent system went from "more agents = more done" to "more agents = more chaos" and back to productive — through explicit protocols for who owns what, how they share state, and what happens at the seams.

---

## The Team Scaling Illusion

Adding agents is seductive. One agent handles your vault. Why not add another for automation pipelines? And a third for real-time conversations?

That's what I did. Three agents, each with a clear specialty:

| Agent | Role | Environment |
|-------|------|-------------|
| **Eve** | The brain — design, reasoning, security-sensitive work | Mac Mini (24/7) |
| **Chloe** | The hands — automation pipelines, data collection | n8n (Docker) |
| **Claude** | The builder — vault management, complex reasoning, content | Claude Code (interactive) |

On paper, this is a clean division of labor. In practice, the first month was chaos.

---

## The Three Coordination Problems

### Problem 1: "Who Owns This?"

The first thing that broke was ownership. When a meeting transcript arrived, who processes it? Eve could reason about it. Chloe's pipeline could route it. Claude could enrich it with vault context.

Without explicit ownership, all three would partially process the same item — or worse, none would, each assuming another had handled it.

This is the organizational equivalent of the **diffusion of responsibility** problem. In a team with unclear roles, urgent tasks get done three times and important tasks get done zero times.

**The fix: Explicit routing rules.**

We didn't need a sophisticated coordination protocol. We needed a simple routing table:

| Input Type | Owner | Others |
|-----------|-------|--------|
| Incoming message (Telegram) | Eve (first responder) | Claude (escalation) |
| Scheduled pipeline (cron) | Chloe (executor) | Eve (error handler) |
| Interactive session (terminal) | Claude (primary) | Eve (async review) |
| Inbox classification | Claude (/inbox command) | Chloe (pre-processing) |

The key insight: **ownership isn't about capability — it's about environment.** Eve owns real-time because she runs 24/7. Chloe owns pipelines because she lives in n8n. Claude owns interactive work because he runs in the terminal. The routing follows the infrastructure, not the skill.

### Problem 2: "What Just Happened?"

The second problem was context fragmentation. Each agent operated in its own session with its own context window. When Eve made a decision at 2 AM, Claude didn't know about it when the user started a morning session.

The symptoms were maddening:
- Claude would propose work that Eve had already completed overnight
- Chloe's pipeline would process items that Claude had manually handled
- Eve would review a document using outdated context because she didn't know Claude had revised it

In org terms: three employees who never cc each other on emails.

**The fix: Shared state, not shared context.**

We couldn't share context windows — each agent has its own. Instead, we built three shared state mechanisms:

**1. The Future Board (shared task tracker)**

A markdown file that all agents read and write. Every task has an owner, status, and handoff history:

```markdown
| FUTURE-20260322-001 | Dashboard v2 | Claude | WIP | 2026-03-22 |
  Handoff: "2026-03-22 [Claude→Eve]: Phase 1 done, review layout"
  Handoff: "2026-03-22 [Eve→Claude]: Layout approved, proceed to Phase 2"
```

When any agent starts a session, they read the Future Board first. This eliminates "I didn't know you were working on that."

**2. Conversation logs (JSONL per agent)**

Each agent writes a structured log of every session:

```jsonl
{"ts":"2026-03-26T02:15:00+09:00","agent":"eve","role":"system","content":"session_start"}
{"ts":"2026-03-26T02:16:00+09:00","agent":"eve","role":"assistant","content":"Processed 5 inbox items, routed 3 to projects"}
```

When Claude starts a morning session, `/start` scans Eve's overnight logs for relevant changes. Not the full conversation — just the structured summary.

**3. The CLAUDE.md as shared constitution**

All three agents reference the same CLAUDE.md. This ensures they make decisions using the same judgment framework, follow the same risk gates, and respect the same autonomy boundaries. Without this, each agent would develop its own interpretation of "is this safe to do autonomously?"

### Problem 3: "The Handoff Dropped"

The third problem was the most insidious: handoff failure. Agent A starts a task, reaches a stopping point, and needs Agent B to continue. The information that passes between them determines whether the continuation is seamless or a restart.

**The failure mode**: Agent A writes "Task mostly done, just needs finishing." Agent B reads this, has no idea what "mostly done" means, re-does 60% of the work, and introduces inconsistencies with what A already completed.

In org terms: a shift change with no handoff notes.

**The fix: Structured handoff protocol.**

Every handoff follows a template:

```markdown
## Handoff — [timestamp]
> [one-line summary of what was accomplished]

## Completed
- [specific items, with file paths]

## In Progress
- [what's partially done, with current state]

## Next Steps
- [ ] [specific, actionable items]

## Context
- [decisions made and why]
- [files created or modified]
- [blockers discovered]
```

The critical section is **Context** — not what was done, but *why*. Without the rationale, the receiving agent makes different assumptions and the work diverges.

We also learned a painful lesson about handoff *direction*. Early on, handoffs were written for the sending agent ("here's what I did"). They should be written for the *receiving* agent ("here's what you need to know to continue"). The difference is subtle but transforms usability.

---

## Conflict Resolution: When Agents Disagree

With three agents, disagreements are inevitable. Eve might classify a document as "archive-ready" while Claude sees it as "needs enrichment." Chloe's pipeline might route an item to a project that Eve thinks is the wrong destination.

We handle this with a simple hierarchy:

**1. Check the shared state.** If the Future Board assigns ownership, the owner decides. No debate.

**2. Check the judgment framework.** If there's no clear owner, both agents score the decision using the 5-axis framework. The higher score wins — with the reasoning documented.

**3. Escalate to human.** If scores are within 0.5 points (a genuine toss-up), flag it for the user. "Eve recommends X (score 3.8), Claude recommends Y (score 3.6). Your call."

In practice, Step 3 happens rarely (~5% of conflicts). Most disagreements resolve at Step 1 (clear ownership) or Step 2 (one option is objectively better when scored).

---

## Five Principles for Multi-Agent Systems

### 1. Route by Environment, Not Capability

Don't assign tasks based on what an agent *can* do. Assign based on where the agent *lives*. The 24/7 server agent handles async work. The pipeline agent handles scheduled jobs. The interactive agent handles user sessions. Capability overlap is fine — routing clarity is essential.

### 2. Share State, Not Context

You can't merge context windows. You can share structured state: task boards, JSONL logs, and a common constitution. Design the shared state to answer one question: "What changed since I last checked?"

### 3. Write Handoffs for the Receiver

Every handoff should answer: "If I knew nothing about this task, what would I need to continue it?" Include the *why* behind decisions, not just the *what*. File paths, rationale, and explicit next steps — not summaries.

### 4. Make Ownership Unambiguous

If two agents could plausibly own a task, exactly one of them must be designated. The designation method matters less than the fact that it exists. We use a simple routing table. You could use priority rules, round-robin, or capability matching. Just make it deterministic.

### 5. Design for the Seams

The failure mode of multi-agent systems isn't inside any single agent — it's at the *seams* between them. The handoff moment. The status check. The conflict resolution. Spend your design effort on the transitions, not the individual performances.

---

## When to Go Multi-Agent (and When Not To)

Multi-agent systems add coordination overhead. That overhead is only worth it when:

| Condition | Single agent | Multi-agent |
|-----------|-------------|-------------|
| All work is interactive | Sufficient | Overkill |
| Need 24/7 async processing | Insufficient | Required |
| Work spans multiple environments | Awkward | Natural fit |
| Volume exceeds one session's capacity | Bottleneck | Scalable |

If your entire workflow happens in a single terminal session, one agent is better than three. The coordination cost of multiple agents only pays off when you need *environmental diversity* (different runtimes, different schedules) or *throughput* beyond a single session.

---

## Key Takeaway

Multi-agent systems fail at the seams: ownership ambiguity, context fragmentation, handoff drops. The solution isn't smarter agents — it's better **organizational infrastructure**: routing tables, shared state, structured handoffs, and a common constitution.

```
Ch1: Design ability > coding ability
Ch2: CLAUDE.md = constitution
Ch3: Skills = job descriptions
Ch4: Judgment framework = decision-making policy
Ch5: Multi-agent ops = team coordination (routing, shared state, handoffs)
```

The system is nearly complete. One challenge remains: what happens when 166 assets need managing, versioning, and occasionally retiring? That's the scaling problem.

---

**Previous: [Chapter 4 — Judgment Framework](ch04-judgment-framework.md)** — Teaching agents to make decisions with quantified values.

**Next: [Chapter 6 — Asset Management](ch06-asset-management.md)** — What breaks when the system grows from 10 to 166 assets, and how to design around it.
