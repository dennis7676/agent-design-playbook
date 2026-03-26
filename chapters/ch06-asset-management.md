# Chapter 6: Asset Management — What Breaks When the System Grows

> At 10 assets, you can keep everything in your head. At 50, you start losing track. At 166, you need a **system to manage the system.**

## TL;DR

Growing from a few automations to 166 production assets surfaces problems that don't exist at small scale: skills that overlap, workflows that nobody remembers building, decision records that contradict each other. This chapter maps the three scaling walls I hit — at 20, 50, and 100 assets — and the management infrastructure that got me past each one.

---

## The Three Scaling Walls

Scaling isn't linear. You don't gradually feel the weight of complexity. Instead, you hit walls — sudden moments where what worked yesterday stops working today. Each wall requires a different response.

### Wall 1: The Naming Problem (~20 assets)

The first wall is embarrassingly simple: you can't find things.

At 10 assets, you remember what each one does. At 20, you start confusing `n8n-code-nodes` with `n8n-code-and-expressions`. Was the inbox classifier in the skill or the workflow? Did I put the meeting template in `templates/` or `skills/`?

This isn't a capability problem. It's a **naming and discovery** problem.

**What broke**: I built a new skill for something that already existed — twice. Each version had slight differences, and I didn't realize the duplication until all three were triggering on the same inputs and producing subtly different outputs.

**The fix: A naming convention with machine-checkable rules.**

```
Skills:     [domain]-[function]     → obsidian-writing-rules, n8n-code-and-expressions
Workflows:  [trigger]_[action]      → Gmail_SK_Processor, Daily_Review
Commands:   /[verb]                 → /inbox, /connect, /wrap
Templates:  [type]-note.md          → daily-note.md, project-card.md
```

The convention isn't just for humans. It enables automated duplicate detection — the quality gate can flag when two skills share 80%+ of their trigger keywords.

In org terms: at 20 employees, you need a company directory. Not because people can't introduce themselves, but because you can't rely on everyone knowing everyone.

### Wall 2: The Dependency Problem (~50 assets)

The second wall is about invisible connections.

At 50 assets, changing one thing breaks another. You modify the inbox classification logic, and three downstream workflows stop receiving the inputs they expect. You update a frontmatter schema, and the knowledge graph validator starts flagging false positives.

**What broke**: I updated a webhook endpoint format. The change was correct — but I didn't realize four workflows depended on the old format. Three of them failed silently. The fourth produced corrupted data for two days before I noticed.

**The fix: A registry with dependency tracking.**

The Asset Registry isn't just a list. It's a **dependency map**:

| Asset | Type | Depends On | Depended By |
|-------|------|-----------|-------------|
| obsidian-writing-rules | skill | — | /inbox, /connect, meeting-enrichment |
| meeting-enrichment | skill | obsidian-writing-rules, people-google-sync | /inbox (Phase 2) |
| Gmail_SK_Processor | workflow | — | inbox classification |

Before changing any asset, I now check: "What depends on this?" If the answer is "3+ other assets," the change gets an L4 risk rating — backup first, then modify, then verify downstream.

In org terms: at 50 employees, you need a process map. Not because processes are complex, but because the connections between them are invisible until something breaks.

### Wall 3: The Coherence Problem (~100 assets)

The third wall is the most subtle. Individual assets work. The system as a whole doesn't make sense.

At 100+ assets, you have skills that were built for different eras of the system. Early skills assume one workflow structure. Recent skills assume another. Some decision records contradict later ones. The overall system is a geological record of past thinking — layers of decisions that made sense at the time but don't form a coherent whole.

**What broke**: A new team member (well, a new agent) tried to onboard using the system documentation. It couldn't resolve the contradictions between early and late design decisions. "The judgment framework says X, but decision D-047 says Y, and skill Z implements something different from both."

**The fix: Periodic coherence audits.**

Every two weeks, I run a system-level audit that checks:

1. **Skill overlap**: Do any two skills share >50% trigger keywords?
2. **Decision consistency**: Do recent ADRs contradict earlier ones?
3. **Schema alignment**: Do all assets use the current frontmatter schema?
4. **Dead references**: Do any assets reference files or endpoints that no longer exist?

The audit produces a debt score. Red items get fixed immediately. Yellow items get scheduled. Green items are healthy.

In org terms: at 100 employees, you need an organizational review. Not a performance review of individuals, but a structural review of how the whole system fits together. Are teams aligned? Are policies consistent? Are processes still serving their original purpose?

---

## The Asset Lifecycle

Every asset goes through a lifecycle. Understanding this lifecycle prevents the most common management failure: keeping dead assets alive.

```
Draft → Active → Stable → Stale → Archived
```

| Stage | Signal | Action |
|-------|--------|--------|
| **Draft** | Recently created, <3 uses | Monitor, expect changes |
| **Active** | Regular use, still evolving | Normal operation |
| **Stable** | Consistent use, rarely changed | Low maintenance |
| **Stale** | No use for 30+ days | Review: still needed? |
| **Archived** | Confirmed unnecessary | Remove from active load |

The critical transition is **Stale → Archived**. Most people skip this, leaving dead skills loaded in every context window, wasting tokens and creating confusion.

The trigger for staleness review is simple: if an asset hasn't been referenced in 30 days, flag it. Maybe it's seasonal (monthly reports). Maybe it's dead. The review determines which.

---

## What Actually Scales

Not everything needs to scale. Here's what I've learned about what to invest in versus what to keep simple:

### Invest in scaling: Discovery infrastructure

- Asset registry (who has what)
- Naming conventions (how to find it)
- Duplicate detection (is this already built?)
- Dependency maps (what connects to what)

These pay dividends at every size. A naming convention that works for 20 assets still works for 200.

### Keep simple: Governance

- Manual coherence audits (automated detection, manual resolution)
- Human-approved archival (don't auto-delete; auto-flag, human-decide)
- Version control through git, not custom versioning systems

The temptation is to build elaborate governance automation. Resist it. A biweekly audit script that takes 5 minutes to review is better than a continuous monitoring system that generates noise.

### Automate: Routine checks

- Schema validation (does this file have the right frontmatter?)
- Link integrity (do all references resolve?)
- Trigger overlap detection (do two skills fire on the same input?)

These are the high-frequency, low-judgment tasks that automation handles well. They catch drift before it becomes debt.

---

## Five Principles for Asset Management at Scale

### 1. Registry Before Building

Before creating a new asset, check the registry. Is something similar already built? Can an existing asset be extended? The cheapest asset is the one you don't build.

### 2. Name for Machines, Not Just Humans

`helper-v2-final-FINAL.md` is a human naming pattern. `email-enrichment/SKILL.md` is a machine-parseable pattern. Name assets so that automated tools can detect duplicates, map dependencies, and audit coverage.

### 3. Track Dependencies, Not Just Assets

The registry isn't a flat list. It's a graph. Every asset should declare what it depends on and what depends on it. This turns "what does changing X break?" from a mystery into a lookup.

### 4. Schedule Staleness Reviews

Set a cadence (biweekly works for me) to review flagged stale assets. The review takes 5 minutes: skim the list, confirm each is truly unused, archive the dead ones. Without this cadence, stale assets accumulate until the system is 40% dead weight.

### 5. Coherence > Completeness

A system with 80 coherent assets outperforms a system with 166 contradictory ones. When forced to choose between "build the next thing" and "align what exists," choose alignment. The compound interest of coherence — every new asset building cleanly on existing infrastructure — outweighs the marginal value of one more skill.

---

## The Curator's Mindset

This brings us back to the third thesis from Chapter 1:

> *"Curation is narrative design, not quality control."*

At 166 assets, the challenge isn't making each one good. It's making them tell a **coherent story** — why these skills exist, how they relate to each other, what they're collectively trying to achieve.

A pile of excellent tools is still a pile. A curated system — where each component has a clear role, defined relationships, and a reason for existence — is an architecture.

The difference is the narrative: not "I have 166 assets" but "I have a system that handles collection, processing, reasoning, and output, with 166 components organized into 5 layers, governed by a shared judgment framework, and maintained through periodic coherence audits."

That narrative is the final design artifact. And maintaining it — keeping the system's story true as it evolves — is the ongoing work of the agent designer.

---

## Key Takeaway

The complete progression:

```
Ch1: Design ability > coding ability
Ch2: CLAUDE.md = constitution (Map → Guardrails → Cognition)
Ch3: Skills = job descriptions (scope, triggers, boundaries)
Ch4: Judgment framework = decision-making policy (values, gates, autonomy)
Ch5: Multi-agent ops = team coordination (routing, shared state, handoffs)
Ch6: Asset management = organizational scaling (registry, lifecycle, coherence)
```

Six aspects of one insight: **designing an AI agent system is organizational design for digital workers.** The same principles that make human organizations effective — clear roles, documented decisions, appropriate autonomy, coherent structure — make agent systems effective.

The difference is that with agents, you get to design the organization from scratch, iterate daily, and document every decision. That's the opportunity. This playbook is an attempt to show what that looks like in practice.

---

**Previous: [Chapter 5 — Multi-Agent Operations](ch05-multi-agent-ops.md)** — Team coordination across three agents.

**Back to: [README](../README.md)** — Start from the beginning, or explore the [templates](../templates/) and [worksheets](../worksheets/).
