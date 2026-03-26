# Agent Design Playbook

> A practical guide to designing AI agent systems — not from theory, but from building and operating 166 production assets over 2 months.

## Why This Exists

The market is polarized:
- **Theory side**: Anthropic's "Building Effective Agents" eBook, Packt textbooks
- **Code side**: LangGraph tutorials, CrewAI quickstarts

What's missing: **"Why did you choose this structure and reject that one?"** — the design decisions behind real systems.

This playbook fills that gap with:
- **166 production assets** (20 skills, 77 workflows, 25 commands, 7 MCP servers)
- **150+ architectural decisions** documented in ADRs
- **3 permanent notes** forming the thesis, refined through daily operation

## Who It's For

| Audience | What You'll Get |
|----------|----------------|
| **PM / Non-developer** | How to think about agent design without writing code |
| **Solo Builder** | Patterns for scaling from 1 agent to a multi-agent system |
| **Career Transitioner** | Why "designing agents well" is the new core skill |

## Chapters

| Ch | Title | Key Question | Evidence Base |
|----|-------|-------------|---------------|
| 1 | [Why Design Ability?](chapters/ch01-why-design-ability.md) | Why is coding giving way to agent design? | Karpathy's "skill issue" + author's transition story |
| 2 | [CLAUDE.md Design](chapters/ch02-claude-md-design.md) | What does context engineering look like in practice? | 20+ revisions of a real CLAUDE.md over 25 days |
| 3 | [Skill Architecture](chapters/ch03-skill-architecture.md) | What separates a good skill from a bad one? | 20 skills — success/failure pattern comparison |
| 4 | [Judgment Framework](chapters/ch04-judgment-framework.md) | How do you teach an agent to make decisions? | 5-axis scoring system + 3 gate rules in production |
| 5 | [Multi-Agent Operations](chapters/ch05-multi-agent-ops.md) | What happens when 3 agents share a workspace? | Eve/Chloe/Claude handoff patterns + shared state |
| 6 | [Asset Management](chapters/ch06-asset-management.md) | What breaks when the system grows? | 166 assets → registry, versioning, deprecation |

## Project Structure

```
agent-design-playbook/
├── chapters/          # Blog posts synced as chapters
├── templates/         # Reusable CLAUDE.md, SKILL.md, workflow templates
├── case-studies/      # Real architectural decisions with context
└── worksheets/        # Study group exercises
```

## The Three Theses

This playbook is built on three interconnected claims, each backed by permanent notes refined over months of operation:

1. **"The skill of the AI era is designing agents well"** — Not prompting. Not coding. Designing the system that makes agents effective.

2. **"Systematic organization beats individual heroics"** — A structured approach to agent management outperforms ad-hoc brilliance.

3. **"Curation is narrative design, not quality control"** — The value isn't in each piece being good. It's in the whole system telling a coherent story.

## Evidence, Not Opinion

Every recommendation in this playbook is backed by:

| Asset Type | Count | Examples |
|-----------|-------|---------|
| Claude Code Skills | 20 | obsidian-writing-rules, deep-research, autoresearch-loop |
| n8n Workflows | 77 | Content pipeline, meeting enrichment, inbox classification |
| Slash Commands | 25 | /start, /wrap, /inbox, /connect, /weekly |
| MCP Integrations | 7 | Gmail, Calendar, Notion, n8n, Slack |
| Architectural Decisions | 150+ | Documented in decisions.md with rationale |

## Getting Started

**If you're reading the playbook**: Start with [Chapter 1](chapters/ch01-why-design-ability.md) — it sets up the "why" before diving into the "how."

**If you want to build**: Jump to [templates/](templates/) for starter CLAUDE.md and SKILL.md files you can fork and adapt.

**If you're running a study group**: See [worksheets/](worksheets/) for structured exercises with discussion prompts.

## Status

| Phase | Scope | Status |
|-------|-------|--------|
| **P1 MVP** | Ch 1-2 + repo setup | **In Progress** |
| P2 | Ch 3-4 + skill templates | Planned |
| P3 | Ch 5-6 + study materials | Planned |
| P4 | Feedback + bilingual (KR/EN) | Planned |

## License

MIT

## Author

Built by someone who went from "How do I prompt this?" to operating a 166-asset agent system — and documented every decision along the way.

---

*"The best agents aren't built. They're designed."*
