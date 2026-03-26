# Chapter 4: Judgment Framework — Teaching Agents to Make Decisions

> The hardest thing to teach an agent isn't *what* to do. It's *how to decide* when there's no right answer.

## TL;DR

Agents face dozens of judgment calls per session: which task first, which approach, how much autonomy. Most people handle this by either micromanaging (approve everything) or hoping for the best (approve nothing). This chapter shows a third way: a 5-axis scoring system refined through 15+ iterations, with gate rules that prevent catastrophic decisions and autonomy boundaries that graduate trust based on reversibility.

---

## The Decision Problem

Here's a scenario that happens daily:

You ask your agent to "clean up the inbox." The inbox has 32 items. Some are emails that need project routing. Some are research notes that could become permanent knowledge. Some are stale drafts that should probably be archived.

The agent needs to make 32 decisions. For each one:
- Where does this go?
- How confident am I?
- Should I ask, or should I act?

Without a judgment framework, the agent has two modes:

**Mode 1: Ask everything.** "Should I route this email to the biometric project? Should I archive this draft? Should I..." — 32 interruptions later, you've done the work yourself with extra steps.

**Mode 2: Guess everything.** The agent routes all 32 items based on keyword matching. 28 are correct. 4 are catastrophically wrong — a contract document lands in archives, a personal note goes to the work project.

Neither mode is acceptable. What you actually want is Mode 3: **"Make the easy calls autonomously. Flag the hard calls. Never make the dangerous calls without asking."**

That requires *judgment*. And judgment doesn't come from better prompting — it comes from a framework.

---

## Designing Judgment: The Values-First Approach

Most decision frameworks start with rules: "if X, do Y." That works for simple systems. But agents face ambiguous situations where no rule directly applies. In those moments, they need something deeper than rules — they need *values*.

Here's the uncomfortable part: teaching an agent your values means first knowing what they are.

I went through an exercise I wouldn't have expected to do for a productivity system. I analyzed years of my own decisions — documented in chat logs, meeting notes, and architectural decision records — looking for patterns. What did I consistently prioritize? What did I consistently sacrifice?

The answer crystallized into five axes, each with a weight:

| Axis | Weight | Core Question |
|------|--------|--------------|
| **Consistency** | 0.34 | Does this align with existing principles and context? |
| **Feasibility** | 0.26 | Can we actually execute this with current resources? |
| **Relationship Safety** | 0.18 | Does this protect people and team energy? |
| **Reversibility** | 0.12 | If this fails, can we undo it? |
| **Aesthetic Fit** | 0.10 | Does this feel right — not just functionally, but *culturally*? |

The weights aren't arbitrary. They emerged from analyzing which axis I consistently prioritized when two values conflicted. Consistency beats feasibility (I'd rather delay a feasible plan that contradicts our principles than rush an inconsistent one). Feasibility beats relationships (I won't avoid hard conversations to preserve comfort, but I will delay them to find a better framing). And so on.

In organizational terms: this is the **decision-making policy**. Every company has one, whether written or not. The written ones produce consistent decisions across teams. The unwritten ones produce chaos and politics.

---

## The Three Layers of Judgment

The framework evolved through three iterations, each triggered by a specific class of failure.

### Layer 1: Scoring (Quantified Judgment)

The first version was simple: score every decision on five axes, 0-5 each, apply weights, get a total.

```
TotalScore = (P0 × 0.34) + (P1 × 0.26) + (P2 × 0.18) + (P3 × 0.12) + (P4 × 0.10)
```

This immediately improved option comparison. Instead of "Option A seems better," the agent could say "Option A scores 4.2, Option B scores 3.8 — A is stronger on consistency (5 vs 3) but weaker on feasibility (3 vs 5)."

**The failure that triggered Layer 2**: The scoring system treated all decisions equally. A file rename and a production workflow change both got the same 5-axis analysis. But they're fundamentally different — one is instantly reversible, the other could break a running system.

### Layer 2: Gates (Automatic Stop Rules)

Layer 2 added three gates — conditions that override the score and force a specific outcome:

| Gate | Condition | Result | Why |
|------|-----------|--------|-----|
| Gate-1 | Consistency < 2.0 | **REJECT** | Contradicts core principles — no score compensates |
| Gate-2 | Feasibility < 2.5 | **HOLD** | Can't execute — redesign first |
| Gate-3 | Relationship Safety < 2.0 (no mitigation plan) | **HOLD** | Risk of damage without a plan to address it |

The gates encode an insight about decision-making: **some failures are categorical, not gradual.** A decision that contradicts your core principles doesn't become acceptable just because it's highly feasible and aesthetically pleasing. It's a category error, and no amount of points on other axes fixes it.

**The failure that triggered Layer 3**: The gates prevented catastrophic decisions but didn't help with *autonomy*. The agent still asked for approval on everything from file moves to inbox classification. The system needed a way to say "you can handle this yourself" vs. "check with me."

### Layer 3: Autonomy Boundaries (Graduated Trust)

Layer 3 defined a five-level risk classification:

| Level | Action Type | Gate |
|-------|------------|------|
| L1-L2 | Read, local reversible changes | Autonomous |
| L3 | Delete, structural changes | Confirm first |
| L4 | External system changes (workflows, APIs) | Backup → Confirm |
| L5 | External publishing, cost-incurring | Human approval required |

The decision rule is elegant: **"Can I undo this in 5 minutes? Does it affect anything outside the workspace?"**

Two questions. If both answers are "yes, reversible" and "no, local only" → autonomous. If either answer flags risk → escalate.

This transformed the agent's behavior. Inbox classification (L1-L2) happens silently. File deletions (L3) get a confirmation prompt. Workflow modifications (L4) trigger an automatic backup first. External publishing (L5) always waits for explicit approval.

In organizational terms: Layer 1 is the decision-making framework. Layer 2 is the compliance policy. Layer 3 is the delegation matrix. Together, they answer "how to decide," "when to stop," and "who decides."

---

## What the Framework Actually Looks Like in Action

Here's a real example. An agent encounters a task: "Should we adopt this new GitHub repository as a tool?"

**Step 1: Score it.**

| Axis | Score | Rationale |
|------|-------|-----------|
| P0 Consistency | 4 | Aligns with existing tool evaluation process |
| P1 Feasibility | 3 | Requires Docker setup, moderate effort |
| P2 Relationship Safety | 5 | No team impact |
| P3 Reversibility | 4 | Can remove if it doesn't work |
| P4 Aesthetic Fit | 4 | Clean API, good documentation |
| **Total** | **3.86** | |

**Step 2: Check gates.**

- P0 (4) ≥ 2.0 → PASS
- P1 (3) ≥ 2.5 → PASS
- P2 (5) ≥ 2.0 → PASS

All gates pass.

**Step 3: Check autonomy.**

This is a tool adoption decision (L3-L4 depending on deployment). The agent presents the scoring to the user with a recommendation, rather than deciding unilaterally.

**Output**: "Recommend adoption (3.86). Strong on consistency and safety, moderate on feasibility. Suggest a pilot deployment first."

Compare this to the alternative: "Should we use this tool?" "Uh, it looks good?" — no structure, no traceability, no way to learn from the decision later.

---

## The Decision Pattern Library

After 150+ documented decisions, patterns emerged. Not every decision needs full 5-axis scoring — many fall into recognizable categories:

| Pattern | Tendency | Gate Emphasis |
|---------|----------|--------------|
| **Tool/Infrastructure** | Pilot first, avoid full replacement | Feasibility gate (P1) strongest |
| **Process Standardization** | Single source of truth, reference-based | Consistency gate (P0) strongest |
| **Communication** | Buffer before responding, reframe before escalating | Relationship gate (P2) strongest |

These patterns are the organizational equivalent of **management training** — not rigid rules, but calibrated intuitions. "When you see a tool adoption decision, you'll usually want to pilot first" is more useful than "always pilot" or "use your judgment."

The patterns feed into the Cognitive Twin protocol from Chapter 2 — when the agent encounters a 50:50 decision, it checks these patterns before guessing.

---

## Five Principles for Building Your Judgment Framework

### 1. Start With Your Own Decisions, Not Best Practices

Generic frameworks ("prioritize impact over effort") are useless because they don't encode *your* specific value trade-offs. Analyze your own decisions: when you sacrificed speed for quality, when you chose simplicity over completeness, when you delayed to protect a relationship. Those trade-offs *are* your framework.

### 2. Weights Must Come From Conflict

You discover your true weights when two values conflict. "I value both consistency and feasibility" is useless. "When they conflict, I choose consistency 67% of the time" — that's a weight (0.34 vs 0.26).

### 3. Gates Prevent Catastrophes; Scores Handle Trade-offs

Don't try to make one mechanism do both jobs. A score of 2.1 on consistency shouldn't be treated the same as 2.1 — the gate exists to say "this is a different kind of problem." Scores are for comparison. Gates are for safety.

### 4. Autonomy Should Be Reversibility-Based

Not role-based ("the agent can't do X"), not complexity-based ("simple tasks are autonomous"), but reversibility-based. A complex task that's easy to undo should be more autonomous than a simple task that's impossible to reverse.

### 5. Document Every Decision

The framework improves through calibration — comparing what the framework recommended vs. what you actually chose. Without documented decisions, there's no data for calibration. My `decisions.md` has 150+ entries. Each one is a calibration point.

---

## The Uncomfortable Truth

Building a judgment framework forces you to articulate things you'd rather leave implicit. What do you actually value? What would you sacrifice? How much risk is acceptable?

Most people avoid this because it's hard. The result is agents that either do too much (and occasionally break things) or do too little (and require constant supervision).

The judgment framework is the middle path: **explicit values, quantified trade-offs, graduated trust.** It won't make every decision correct. But it will make every decision *traceable* — and traceable decisions are the ones you can learn from.

---

## Key Takeaway

The progression through this playbook:

```
Ch1: Design ability > coding ability
Ch2: CLAUDE.md = constitution (Map → Guardrails → Cognition)
Ch3: Skills = job descriptions (precise scope, clear boundaries, testable output)
Ch4: Judgment framework = decision-making policy (values, gates, autonomy)
```

With structure (CLAUDE.md), roles (skills), and decision-making (judgment framework) in place, you have the core of a functioning agent system. But what happens when it's not one agent — it's three? That's the multi-agent coordination problem, and it's next.

---

**Previous: [Chapter 3 — Skill Architecture](ch03-skill-architecture.md)** — What separates good skills from bad ones.

**Next: [Chapter 5 — Multi-Agent Operations](ch05-multi-agent-ops.md)** — What happens when 3 agents share a workspace? Handoff protocols, conflict resolution, and shared state.
