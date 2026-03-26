# Worksheet 6: Asset Health Check

> Companion to [Chapter 6 — Asset Management](../chapters/ch06-asset-management.md)

## Before You Start

This worksheet is a practical health check for your current assets. Run it monthly (or whenever things feel "messy") to catch drift before it becomes debt.

---

## Exercise 1: Asset Inventory (15 min)

List all your AI assets. Be exhaustive — the ones you forget about are usually the problem.

| # | Asset name | Type | Last used | Last updated | Status |
|---|-----------|------|----------|-------------|--------|
| 1 | | skill / workflow / command / template / integration | | | active / stale / unknown |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |
| ... | | | | | |

**Count**: Total ___. Active ___. Stale ___. Unknown ___.

---

## Exercise 2: Staleness Review (10 min)

For each asset marked "stale" or "unknown":

| Asset | Last used | Still needed? | Action |
|-------|----------|-------------|--------|
| | | Yes → reactivate / No → archive / Unsure → test | |
| | | | |
| | | | |

**The 30-day rule**: If unused for 30+ days and not seasonal, it's a candidate for archival.

---

## Exercise 3: Dependency Map (15 min)

For your 5 most critical assets, map dependencies:

```
[Asset A] ──depends on──→ [Asset B]
[Asset A] ──depended by──→ [Asset C]
```

| Asset | Depends on | Depended by | Risk if changed |
|-------|-----------|-------------|----------------|
| | | | Low / Medium / High |
| | | | |
| | | | |
| | | | |
| | | | |

**High-risk assets** (3+ dependents): These need backup-before-modify protocols.

---

## Exercise 4: Overlap Detection (10 min)

List any assets that seem to do similar things:

| Asset A | Asset B | Overlap % | Resolution |
|---------|---------|-----------|------------|
| | | __% | Merge / Split roles / Archive one |
| | | | |
| | | | |

**The test**: If you explain both assets to someone, do they ask "what's the difference?"

---

## Exercise 5: Coherence Check (10 min)

Answer these questions honestly:

| Check | Status |
|-------|--------|
| Do all assets follow the same naming convention? | [ ] Yes [ ] Partially [ ] No |
| Do all assets use the current schema/format? | [ ] Yes [ ] Partially [ ] No |
| Can you explain how any asset connects to any other? | [ ] Yes [ ] Mostly [ ] No |
| Are there decision records that contradict each other? | [ ] None [ ] Some [ ] Many |
| Could a new person onboard using just the documentation? | [ ] Yes [ ] Partially [ ] No |

**Scoring**: 5 "Yes" = healthy. 3-4 = normal drift. <3 = coherence audit needed.

---

## Exercise 6: Health Score (5 min)

Calculate your system health:

| Metric | Value | Weight | Score |
|--------|-------|--------|-------|
| Active rate (active / total) | __% | × 0.30 | |
| Freshness (updated in 30 days / total) | __% | × 0.25 | |
| No overlap (non-overlapping / total) | __% | × 0.20 | |
| Dependency documented (mapped / total) | __% | × 0.15 | |
| Naming consistent (following convention / total) | __% | × 0.10 | |
| **Total Health Score** | | | **__/100** |

**Interpretation**:
- 80+ = Healthy system, maintain current cadence
- 60-79 = Normal drift, schedule cleanup
- <60 = Significant debt, prioritize remediation

---

## Monthly Tracking

| Date | Total assets | Active | Stale | Health score | Action taken |
|------|-------------|--------|-------|-------------|-------------|
| | | | | | |
| | | | | | |
| | | | | | |

---

## Discussion Prompts (Study Group)

1. **"What's your health score?"** Compare across the group. What's the biggest contributor to low scores?

2. **"Has anyone done a major cleanup?"** What got archived? How did it feel? Did anything break?

3. **"At what asset count did you first feel overwhelmed?"** Map the group's scaling walls. Are they similar?

---

## Deliverable

By the end, you should have:
- [ ] Complete asset inventory with status
- [ ] Staleness review with action items
- [ ] Top-5 dependency map
- [ ] System health score
- [ ] 3 specific cleanup actions scheduled
