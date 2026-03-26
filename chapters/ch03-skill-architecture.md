# Chapter 3: Skill Architecture — What Separates Good Skills from Bad Ones

> A well-designed skill is a **job description**, not a to-do list. It defines the role, not the tasks.

## TL;DR

Size doesn't determine quality. A 274-line skill got archived for redundancy. An 8,000-token skill thrives because every token is justified. This chapter compares real production skills — the ones that worked and the ones that didn't — to extract five design principles for building skills that actually hold up under daily operation.

---

## The Scope Paradox

Here's something that surprised me.

One of the first skills I archived was 274 lines long — well under the recommended 500-line limit. It was clean, organized, and covered its topic thoroughly. And it was completely unnecessary, because another skill already handled 95% of what it did.

Meanwhile, one of my most reliable skills weighs in at 8,000 tokens — far above any "best practice" guideline. But every token is load-bearing. Remove any section and the research pipeline breaks.

**The lesson**: Skill quality isn't about size. It's about *justification density* — whether every line earns its place. A lean skill full of redundancy is worse than a heavy skill full of necessity.

This maps back to our organizational design metaphor. A good job description isn't short. It's *precise*. It defines exactly what this role does, what it doesn't do, and how it connects to other roles. A bad job description is either so vague that three people think they own the same task, or so overloaded that one person is doing four jobs.

---

## Anatomy of a Failed Skill

Let me show you what failure looks like, using a real example.

### Case Study: The Skill That Got Absorbed

I had a skill called `n8n-code-nodes` that taught the agent how to write JavaScript and Python in n8n's Code nodes. It was well-structured: error patterns, common recipes, language comparison tables. The kind of reference material you'd want at hand.

The problem: I also had `n8n-code-and-expressions`, which covered the same territory plus expression syntax, built-in functions, and debugging patterns. The overlap was ~95%.

**What went wrong**:

| Issue | Symptom |
|-------|---------|
| **Scope overlap** | Two skills competing to answer the same question |
| **Vague trigger** | "Use when writing code OR troubleshooting errors OR choosing between languages" — three different contexts in one OR chain |
| **No boundary** | No "NOT WHEN" section. No clear handoff to the other skill |
| **No necessity** | Removing it broke nothing. The other skill handled everything |

The archived file now reads: *"JS/Python 통합 가이드가 n8n-code-and-expressions에 완전 흡수됨. 고유 콘텐츠 없음."* (Fully absorbed. No unique content.)

In org terms: we had two employees with the same job description, and one of them had to go.

### The Bloat Pattern

The opposite failure is the skill that does too much. One of my skills grew to 6,700 tokens by absorbing related responsibilities: validation logic, people matching, meeting enrichment, and wiki registration — all in one file.

It worked. But maintaining it was a nightmare. Changing the people-matching logic meant loading 6,700 tokens of context, most of it irrelevant. A bug in wiki registration required understanding the entire validation pipeline.

The evaluation report recommended splitting it into focused skills. The core would drop from 7,200 to ~3,000 tokens.

In org terms: one employee doing four jobs. Productive? Technically yes. Sustainable? No.

---

## Anatomy of a Successful Skill

Now let me show you what success looks like.

### Case Study 1: The Rule Book (274 lines, 3,500 tokens)

My `obsidian-writing-rules` skill is a set of vault-wide formatting rules: frontmatter schemas, task formats, filename conventions, capture templates, and consistency checks. Seven sections, each independent.

**Why it works**:

**1. Trigger is syntactically precise.**

Not: "Use when working with Obsidian." That matches everything.
Instead: "Use when writing or modifying any file in the Obsidian vault." One condition, one action, zero ambiguity.

**2. Boundaries are lovingly documented.**

The skill's most important paragraph is a gotcha:

> *When a user sends a URL with "take notes on this," it's easy to misclassify as a memo. But if there's a URL, it MUST be classified as a URL capture — regardless of what the user said.*

That single paragraph prevents a class of recurring misclassifications. It's not a general principle — it's a specific, battle-tested correction documented because it kept happening.

**3. Each section is independently useful.**

You can read Section C (filename conventions) without understanding Section F (knowledge graph checks). This means the agent loads the full skill but only activates the relevant section. No wasted context.

**Test results**: 83% pass rate across structured scenarios. The remaining 17% is an intentionally ambiguous edge case requiring human judgment — the skill correctly identifies it as such.

### Case Study 2: The Research Engine (8,000 tokens)

My `deep-research` skill orchestrates multi-agent research: dispatching Haiku-class collectors in parallel, cross-validating with Sonnet, synthesizing with Opus-class reasoning. Six steps, each clearly isolated.

**Why it works despite its size**:

**1. Token spend maps to model cost.**

The skill's core insight: *"Collect cheap, judge expensive."* It allocates fast, cheap agents for gathering and reserves expensive reasoning for synthesis. The 8,000 tokens teach this allocation strategy — you can't compress that into a checklist.

**2. Explicit "NOT WHEN" section.**

> Quick factual questions → use `web-research` skill
> Simple URL reads → use `firecrawl` directly
> Deep comparative analysis, tool evaluation → use THIS skill

This prevents over-triggering. Without it, every web search would load 8,000 tokens of research pipeline.

**3. Failure limits are built in.**

Step 3.5 includes a circuit breaker: if coverage is still below threshold after maximum rounds, proceed anyway and mark gaps as "unverified." Without this, the skill could loop indefinitely trying to achieve perfect coverage.

---

## Five Principles of Skill Architecture

Drawing from 20+ production skills — the ones that survived and the ones that didn't:

### 1. The Necessity Test

Before building a skill, ask: *"If this skill disappeared tomorrow, what would break?"*

| Answer | Action |
|--------|--------|
| "Nothing, another skill handles it" | Don't build it |
| "Tasks X and Y would become inconsistent" | Build it |
| "I'd have to do Z manually every time" | Build it |

The archived `n8n-code-nodes` would have failed this test on day one. The active `obsidian-writing-rules` passes it decisively — without it, every vault write would produce inconsistent frontmatter.

### 2. Trigger Precision Over Trigger Breadth

**Bad**: `"Use when writing code OR troubleshooting errors OR choosing modes"` — three different contexts collapsed into one trigger.

**Good**: `"Use when [single condition]"` — the skill activates in exactly one scenario, with explicit redirects for adjacent scenarios.

The pattern: good triggers read like `if-then` statements. Bad triggers read like `if-or-or-or-then` statements.

### 3. Gotchas Are Load-Bearing

Here's a counterintuitive finding from comparing token allocation across skills:

| Skill | Lines on examples | Lines on gotchas | Status |
|-------|------------------|-----------------|--------|
| n8n-code-nodes (archived) | ~190 | <10 | Dead |
| obsidian-writing-rules | ~80 | ~100 | Thriving |
| deep-research | ~120 | ~80 | Thriving |

**Successful skills spend more tokens on "what can go wrong" than on "happy path examples."**

Each gotcha should answer four questions:
1. What specific mistake does this prevent?
2. Why is it subtle (not obvious)?
3. What's the fix?
4. When is this gotcha irrelevant (scoping)?

### 4. Output Contracts Must Be Testable

The difference between "generate a summary" and "generate a YAML frontmatter block with these 5 required fields" is testability.

Good skills define outputs that can be mechanically verified:
- **obsidian-writing-rules**: Frontmatter must have `type`, `created`, `updated`, `status`, `tags` → machine-checkable
- **deep-research**: Step 3 produces a coverage score ≥ threshold → numerically verifiable
- **skill-quality-gate**: 5-item checklist, each pass/fail → binary outcome

Bad skills promise vague outputs: "helpful code," "clear explanation," "appropriate response." These can't be tested, so failures are invisible.

### 5. Change History Is a Vital Sign

A skill without version history is either perfect (unlikely) or abandoned (likely).

Active, healthy skills show:
- Version numbers or dated changelog entries
- Evidence of iteration (sections added, rules refined)
- Trigger refinements based on over/under-firing incidents

When evaluating someone else's skill (or your own from three months ago), check the change history first. If the last update was weeks ago and the system has evolved, the skill is probably drifting out of alignment.

---

## The Job Description Test

Here's a practical exercise. Take any skill and apply the "job description test":

1. **Role**: Can you explain what this skill does in one sentence?
2. **Responsibilities**: Are the boundaries clear? Does it overlap with other skills?
3. **Success criteria**: How do you know the skill performed correctly?
4. **Escalation path**: What happens when the skill encounters something outside its scope?
5. **Onboarding**: Could a new agent (or a fresh context window) use this skill effectively from the description alone?

If any answer is "unclear" or "it depends," the skill needs redesign.

In an organization, a vague job description means territorial conflicts, dropped tasks, and duplicated work. In an agent system, a vague skill means the same things — they just manifest as incorrect triggers, inconsistent outputs, and wasted tokens.

---

## Key Takeaway

**Good skills are precise, not small.** They earn every token with necessity, define boundaries through documented failures, and spend more effort on "what goes wrong" than "what goes right."

The progression from Chapter 1 to here:

```
Ch1: Design ability > coding ability
Ch2: CLAUDE.md = constitution (Map → Guardrails → Cognition)
Ch3: Skills = job descriptions (precise scope, clear boundaries, testable output)
```

The system is taking shape. But so far, we've covered structure and roles. The hardest part — teaching the system to *make decisions* — is next.

---

**Previous: [Chapter 2 — CLAUDE.md Design](ch02-claude-md-design.md)** — How a CLAUDE.md evolves from config file to constitution.

**Next: [Chapter 4 — Judgment Framework](ch04-judgment-framework.md)** — How do you teach an agent to make decisions? A 5-axis scoring system iterated 15+ times in production.
