---
name: lordgen-ai-skill-builder
description: Foundation for building the skills that will control the LordGen AI system, plus the reference material those skills draw from.
---

# LordGen AI — Skill builder

This repo has two jobs, deliberately kept in one place:

1. **`skills/`** — the capability to build Claude Code skills, starting with
   `skills/skill-builder/` itself: a general-purpose skill for designing, writing, testing,
   and improving any SKILL.md package to a production quality bar. Every skill LordGen ends
   up running — proposal drafting, outreach prep, reporting, whatever comes next — gets built
   using this.
2. **`references/`** — the business and brand grounding those skills are built against:
   what LordGen actually sells, which platform runs what, what's safe to automate vs. needs a
   human, and the real brand identity. A skill authored without reading the relevant reference
   file first is guessing.

```
Lordgen AI Skill builder/
├── skills/
│   └── skill-builder/       # SKILL.md + references/ + assets/ — the skill-authoring skill
├── references/
│   ├── README.md            # index — which file to read for which kind of skill
│   ├── architecture.md      # mission, the three business loops, layer map
│   ├── platform-map.md      # which connected tool (HubSpot, Apollo, Vercel...) owns what
│   ├── offers.md            # offer catalogue — stub, needs real pricing/scope filled in
│   ├── human-in-the-loop.md # what a skill may automate vs. must hand off for approval
│   └── brand.md             # pointer to the real brand identity + logo files
└── README.md                # this file
```

## What this is not, yet

This is not the full LordGen business-operations repo. There's no `CLAUDE.md` here on
purpose — that belongs to a separate operating-rules setup the user is building elsewhere.
There's no `clients/`, `workflows/`, or `tools/` tree here either; those live in the two
standalone sibling projects for now:

- `../Lordgen ai scraper/` — Firecrawl-based lead-gen scraping, its own `CLAUDE.md`
- `../Newsletter Demo/` — the research → infographic → branded-email pipeline, and the source
  of the real brand assets `references/brand.md` points at

Nothing here merges those in. This foundation exists so that when a new LordGen skill gets
written — here or in either sibling project — it has somewhere real to pull context from
instead of re-deriving it, or worse, inventing it.

## Source material

Everything in `references/` is condensed from `LordGen_Master_Brief_v3.md`
(`Downloads/Lordgen Markdown files/`) — the reconciled brief. An earlier draft
(`LordGen_Master_Brief_for_Claude.md`) proposed a larger, more speculative system; v3
explicitly cuts that down to what one founder can actually run, so it's the one this repo
follows.
