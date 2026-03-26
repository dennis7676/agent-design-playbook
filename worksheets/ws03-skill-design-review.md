# Worksheet 3: Skill Design Review

> Companion to [Chapter 3 — Skill Architecture](../chapters/ch03-skill-architecture.md)

## Before You Start

This worksheet helps you evaluate existing skills or design new ones using the five principles from Chapter 3.

---

## Exercise 1: The Necessity Test (5 min per skill)

Pick 3 skills (yours or from a template). For each:

| Skill name | If removed, what breaks? | Verdict |
|-----------|------------------------|---------|
| | | Keep / Merge / Archive |
| | | Keep / Merge / Archive |
| | | Keep / Merge / Archive |

**Red flags**: "Nothing breaks" or "Another skill handles it" → candidate for archival.

---

## Exercise 2: Trigger Precision Audit (10 min)

Write your skill's current trigger, then evaluate it:

**Current trigger**: `______________________________`

| Check | Pass? |
|-------|-------|
| Single condition (not OR-chained)? | [ ] |
| Includes a "NOT WHEN" section? | [ ] |
| Distinguishes from adjacent skills? | [ ] |
| A new agent could interpret it correctly? | [ ] |

**If any check fails**, rewrite the trigger:

**Improved trigger**: `______________________________`

**NOT WHEN**: `______________________________`

---

## Exercise 3: The Gotcha Inventory (15 min)

For your skill, list every way it could go wrong:

| Gotcha | Why it's subtle | Fix | When irrelevant |
|--------|----------------|-----|----------------|
| 1. | | | |
| 2. | | | |
| 3. | | | |

**Benchmark**: Successful skills have 3+ documented gotchas. If you have fewer, you haven't operated the skill long enough or haven't been tracking failures.

---

## Exercise 4: The Job Description Test (10 min)

Answer these five questions for your skill:

1. **Role** — What does this skill do in one sentence?
   > _______________________________________

2. **Responsibilities** — Does it overlap with any other skill?
   > _______________________________________

3. **Success criteria** — How do you know it performed correctly?
   > _______________________________________

4. **Escalation** — What happens when it encounters something outside scope?
   > _______________________________________

5. **Onboarding** — Could a new agent use it from the description alone?
   > _______________________________________

**Scoring**: 5/5 clear = well-designed. 3-4 = needs refinement. <3 = redesign.

---

## Exercise 5: Build a New Skill (20 min)

Using the [SKILL.md Starter Template](../templates/SKILL-md-starter.md):

1. Define the trigger (single condition)
2. Write the "NOT WHEN" section first
3. List 3 expected gotchas before writing the happy path
4. Define testable output (what can be mechanically verified?)
5. Set a review date (when will you audit this skill?)

---

## Discussion Prompts (Study Group)

1. **"Show your worst skill."** What makes it bad? Can the group identify the failure pattern?

2. **"Gotchas vs. examples — what's your ratio?"** Compare across the group. Who spends more tokens on what?

3. **"Has anyone archived a skill?"** What triggered the archival? What was the signal?
