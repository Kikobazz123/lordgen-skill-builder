---
name: lordgen-architecture
description: What LordGen AI is — mission, the three business loops, and which architectural layer owns what. Read before designing a skill that spans more than one workflow.
---

# LordGen architecture

Read when: a skill needs to know where it fits in the larger system, or which loop its
output feeds into.

## Mission

LordGen is an AI consulting operating system, not a pile of disconnected automations. It
exists to:

1. Generate revenue.
2. Deliver client outcomes.
3. Automate repetitive business operations.
4. Improve continuously through measurement and feedback, not impressions.
5. Turn client work into reusable IP.
6. Reduce founder bottleneck.
7. Keep humans in control of anything consequential.

> **AI reasons; deterministic software executes; humans approve consequential actions.**

Optimize for **maximum useful autonomy with controlled risk** — not maximum autonomy. Every
skill built here should trace back to one of the seven points above; if it doesn't, it's
probably not a LordGen skill.

## The three loops

**A. Revenue Loop** — Lead discovery → qualification → opportunity research → offer selection
→ proposal → outreach → delivery → upsell → retainer → case study → referral. Every
automation earns its place by answering: *does this create, protect, or expand revenue?*

**B. Delivery Loop** — Research → Diagnose → Design → Build → Test → Deploy → Monitor →
Improve → Report → Renew, per client. Strict client isolation: never mix client data,
prompts, credentials, or outputs without explicit authorization.

**C. Self-Improvement Loop** — Observe → Evaluate → Identify failure → Propose improvement →
Test → Human approval → Deploy → Monitor. Proposal-driven and test-gated — LordGen never
silently rewrites its own production behavior.

A skill usually serves exactly one loop. Naming which loop a new skill serves (in its
frontmatter description, implicitly) helps decide if it's duplicating something that already
exists.

## Architectural layers

The brief's full component tree collapses onto a small set of real folders once you cut
speculative scope — most of it doesn't exist yet and shouldn't be built ahead of a paying
client. What's confirmed:

| Layer | Lives in | Status |
|---|---|---|
| Skill Layer | `skills/<name>/SKILL.md` | This repo — active |
| Agent Layer | Orchestration inside future `workflows/*.md` + `tools/*.py` | Not yet built |
| Workflow Layer | `workflows/*.md` | Not yet built here (the two standalone sibling projects have one working example each — see below) |
| Tool / MCP Layer | `tools/*.py` for deterministic code; MCP servers via `claude mcp add` | Partial — see sibling projects |
| Knowledge / Reference Layer | This `references/` folder | This repo — active |
| Data Layer | PostgreSQL via SQLAlchemy/Alembic (planned, not built) | Not yet built |
| Human Approval Layer | [`human-in-the-loop.md`](human-in-the-loop.md) | Defined |

**Working precedent, not yet formalized as a shared `workflows/`/`tools/` tree:**
- `../../Lordgen ai scraper/` — Firecrawl-based lead-gen scraping (WAT framework: workflow →
  agent → deterministic tool script)
- `../../Newsletter Demo/` — research → infographic → branded HTML → Gmail send pipeline

Both are real and working. Neither has been merged into a shared repo yet — that
consolidation is a deliberate later step, not an oversight.

## Changelog
- v1.0 — Initial, condensed from `LordGen_Master_Brief_v3.md` §1, §2, §7
