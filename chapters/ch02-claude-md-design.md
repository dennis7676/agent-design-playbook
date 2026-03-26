# Chapter 2: CLAUDE.md Design — Context Engineering in Practice

> Your CLAUDE.md isn't a config file. It's a **design document that teaches your agent how to think.**

## TL;DR

Context engineering is the real skill behind effective agents. This chapter traces how one CLAUDE.md evolved through 20+ revisions over 14 days — from a 175-line map of the workspace to a 330-line operating system that handles ambiguity, manages risk, and learns from feedback. The three stages of this evolution reveal a pattern anyone can follow.

---

## The Config File Trap

Here's how most people write a CLAUDE.md:

```markdown
# Project: My App
- Language: TypeScript
- Framework: Next.js
- Use tabs, not spaces
- Run tests before committing
```

It works. The agent follows the instructions. But this approach has a ceiling, and you'll hit it the moment your system does anything more complex than write code in a single repository.

The problem is framing. A config file says *"here are the settings."* A constitution says *"here's how to operate — including when things get ambiguous, risky, or novel."*

That framing difference isn't philosophical. It determines whether your agent can handle the 80% of real work that doesn't fit neatly into a settings list.

## One CLAUDE.md, Three Stages, 14 Days

What follows is the real evolution of a CLAUDE.md managing a personal AI agent system (the same 166-asset system from Chapter 1). I'm going to show the actual git diffs — what was added and, more importantly, *why*.

The three stages map to the organizational design metaphor from Chapter 1:

| Stage | Org Analogy | CLAUDE.md Role | Lines |
|-------|-------------|---------------|-------|
| **V1: The Map** | Office floor plan + org chart | "Here's what exists and where things go" | 175 |
| **V2: The Guardrails** | Safety manual + compliance policy | "Here's what not to break" | 206 |
| **V3: The Cognition** | Management training program | "Here's how to think and decide" | 254→330+ |

Let's walk through each one.

---

## V1: The Map (Day 1)

The first version answered one question: **"What is this workspace?"**

```markdown
## Folder Structure (PARA)
| Folder     | Purpose                                    |
|------------|-------------------------------------------|
| 000_System | Templates, scripts, catalogs               |
| 100_Inbox  | Collection (source_type tags)               |
| 200_Projects | Short-term projects (Active→Done)         |
| ...        | ...                                        |

## Tool Roles
| Tool        | Role                                       |
|-------------|-------------------------------------------|
| Obsidian    | Single source of truth                     |
| n8n         | Pipeline — collection → processing         |
| Claude Code | Builder + operator — reasoning, generation |
| Telegram    | Interface — input, alerts, conversations   |

## Judgment Framework
- 5-axis weights: Consistency(0.34) > Feasibility(0.26) > ...
- Gates: P0 < 2.0 → REJECT
```

**What it got right**: Clear territory mapping. The agent knew where files live, what tools do, and how decisions are weighted.

**What it was missing**: No sense of *risk*. The agent treated renaming a file and modifying a production workflow with identical casualness. No concept of *ambiguity* — when the user's intent was unclear, the agent either guessed or asked generic questions.

In org design terms: we had a floor plan and an org chart, but no safety protocols and no management training. Everyone knew where the desks were. Nobody knew what to do when something went wrong.

**The failure that triggered V2**: The agent deleted a file structure during a reorganization without asking first. Individually, each deletion was logical. Collectively, they broke three cross-references that took an hour to repair. The system needed a concept of *reversibility*.

---

## V2: The Guardrails (Day 10)

V2 added 31 lines. But those 31 lines fundamentally changed the agent's behavior. The key addition: **Action Risk Gate**.

```markdown
## Verification Layer (Action Risk Gate)

| Level | Action                        | Gate              |
|-------|------------------------------|-------------------|
| L1-L2 | Read, local reversible change | Autonomous        |
| L3    | Delete, structural change     | Confirm first     |
| L4    | External system change (n8n)  | Backup → Confirm  |
| L5    | External publish, cost-incurring | HITL required  |

Core question: "Can I undo this in 5 minutes?" + "Does this affect anything outside the vault?"
```

The second major addition: a **tool selection matrix with a feedback loop**.

```markdown
### Web/Browser Tool Selection Matrix

| Task                  | Primary         | Fallback        | Never Use       |
|-----------------------|-----------------|-----------------|-----------------|
| URL content extraction| firecrawl scrape| playwright-cli  | —               |
| Web search            | firecrawl search| —               | playwright-cli  |
| Login-required page   | playwright-cli  | firecrawl browser| firecrawl scrape|
| ...                   | ...             | ...             | ...             |

> Tool selection feedback loop: auto-review at session wrap
> → accumulate in tool-selection-feedback.jsonl → golden set at 10+ entries
```

**What changed**: The agent now had a concept of *blast radius*. Before modifying a production workflow, it would create a backup. Before deleting files, it would ask. The "Never Use" column prevented tool mismatches that had caused silent failures.

**The design principle**: Every rule in V2 was traceable to a specific failure. "Backup before L4 changes" existed because a workflow edit once corrupted a running process. "Confirm before L3" existed because of the file deletion incident. **Good guardrails aren't invented. They're extracted from postmortems.**

In org design terms: we now had a safety manual. Employees knew which actions needed a supervisor's sign-off and which they could handle independently. The "5-minute reversibility" test gave everyone a mental model for risk assessment.

**The failure that triggered V3**: The guardrails worked for *clear* situations. But the agent still struggled with *ambiguous* ones. When I typed "정리해" (clean up), did I mean organize the inbox? Reformat a document? Archive old projects? The agent would pick one interpretation and run with it — sometimes correctly, sometimes disastrously. The system needed a way to handle ambiguity *before* acting.

---

## V3: The Cognition (Day 12)

V3 added the **Cognitive Twin protocol** — 48 lines that gave the agent the ability to interpret ambiguity and make judgment calls.

### The Decoder: Resolving Ambiguity

```markdown
### Decoder (Ambiguity Resolution)

When user input meets these conditions:
- Length ≤ 20 chars OR no explicit verb OR unclear target

Execute Resolution Cascade:
1. Phase A (internal graph walk): Match against Cognitive Patterns
   → Check decisions.md for similar situations
   → Reference Permanent Notes → Recent conversation context
2. confidence ≥ 0.85 → Auto-execute. Display "[interpretation] — proceeding"
3. 0.60 ≤ confidence < 0.85 → Soft confirm. "Did you mean X? (based on AP-NNN)"
4. confidence < 0.60 → Ask directly
5. Phase B (HITL): If Phase A insufficient → present candidates with reasoning
```

**Why this matters**: Before the Decoder, the agent had two modes — "do exactly what was said" or "ask a clarifying question." The Decoder added a third mode: **"interpret based on accumulated context, and calibrate your confidence."** The 0.85 threshold means the agent acts autonomously 60% of the time — but asks when it genuinely doesn't know.

### The Predictor: Making Judgment Calls

```markdown
### Predictor (50:50 Decisions)

When two equal options are detected during work
(task order, scope, approach, tool — no technical right answer):
1. Match against Cognitive Patterns (PR-*.md)
2. confidence ≥ 0.80 → Choose + 1-line rationale.
   "Choosing [X] — matches your usual pattern (PR-NNN)"
3. confidence < 0.80 → Present options + mark recommendation
```

**Why this matters**: Every complex task has moments where two approaches are equally valid. Before the Predictor, the agent would either stall ("Which would you prefer?") or pick randomly. Now it draws on 37 documented decision patterns to make the same choice the user would make — and explains its reasoning.

### The Feedback Loop: Learning from Corrections

```markdown
### Feedback Capture (automatic write-back)

- Accepted: No correction → evidence_count += 1, confidence recalculated
- Corrected: User changes outcome → log override + add counter-example
- Override 3+ times → suggest rule split
```

**The design principle**: V3 turned the CLAUDE.md from a static document into a **learning system**. When the agent interprets correctly, its confidence in that pattern increases. When corrected, it records *why* it was wrong. After enough corrections, it suggests splitting the pattern into more specific rules.

In org design terms: V1 was the office floor plan. V2 was the safety manual. V3 is the management training program — teaching the agent not just what to do, but *how to think about what to do*, and *how to learn from mistakes*.

---

## What Keeps Evolving

The CLAUDE.md didn't stop at V3. Subsequent revisions added:

- **Confidence Decay**: Patterns unused for 30+ days automatically lose confidence (stale knowledge shouldn't drive decisions)
- **Devil's Advocate**: When the Predictor is too confident, it checks whether the "break condition" applies and presents the counter-argument
- **Active Unlearning**: Patterns applied >90% of the time get flagged — over-application is a failure mode too
- **Techno-Reflexivity checks**: Three questions asked before building any new automation: *What does this assume? What does this hide? Who will know if it fails?*

Each addition was triggered by a specific operational failure. The document is now 330+ lines — nearly double its original size — but every line carries its weight.

---

## Five Design Principles for Your CLAUDE.md

Drawing from 20+ revisions, here's what I've learned about writing an effective CLAUDE.md:

### 1. Start as a Map, Evolve into a Constitution

Your first version should answer: "What is this workspace?" Folder structure, tool roles, file locations. Don't try to anticipate everything. **The guardrails will come from experience.**

### 2. Every Rule Should Trace to a Failure

If you can't point to a specific incident that a rule prevents, the rule is probably unnecessary. The Action Risk Gate exists because files were deleted. The Decoder exists because ambiguous inputs were misinterpreted. Preemptive rules are usually wrong — reactive rules, extracted from real failures, are usually right.

### 3. Encode Judgment, Not Just Instructions

The difference between "use tabs" and "when faced with an ambiguous request, check these 37 patterns before guessing" is the difference between a config file and a design document. **Instructions tell the agent what to do. Judgment frameworks tell it how to decide.**

### 4. Build Progressive Autonomy

Not every action needs the same level of oversight. The L1-L5 risk classification lets the agent handle routine work independently while escalating novel or risky decisions. The confidence thresholds in the Cognitive Twin do the same for interpretation. **Design for graduated trust, not binary control.**

### 5. Make It a Living Document

A CLAUDE.md that doesn't change is either perfect (unlikely) or stale (likely). Build feedback loops: tool selection reviews, pattern confidence decay, correction tracking. **The document should get smarter as the system operates.**

---

## Starter Template

If you want to start building your own CLAUDE.md, we've included a [starter template](../templates/CLAUDE-md-starter.md) in this repo. It covers the V1 essentials — folder structure, tool roles, and basic operation rules. As you operate your system and encounter failures, you'll naturally evolve toward V2 (guardrails) and V3 (cognition).

The template is intentionally minimal. **The best CLAUDE.md is the one you grow through experience, not the one you copy from someone else.**

---

## Key Takeaway

A config file is flat: settings in, behavior out. A well-designed CLAUDE.md is *layered*:

```
Layer 1 (Map):       What exists? Where does it go?
Layer 2 (Guardrails): What's risky? How to stay safe?
Layer 3 (Cognition):  How to interpret? How to decide? How to learn?
```

Each layer builds on the one below. You can't write good guardrails without knowing the territory. You can't teach judgment without knowing the risks. **Start with the map. Let the failures teach you the rest.**

---

**Previous: [Chapter 1 — Why Design Ability?](ch01-why-design-ability.md)** — Why "designing agents well" is the core skill of the AI era.

**Next: [Chapter 3 — Skill Architecture](ch03-skill-architecture.md)** — What separates a good skill from a bad one? A pattern comparison from 20 production skills.
