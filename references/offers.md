---
name: lordgen-offers
description: LordGen's productized offer catalogue — candidate offers and the fields each needs before a skill can draft a real proposal from it. Read before building any proposal, scoping, or pricing skill.
---

# Offer catalogue

Read when: a skill drafts a proposal, scopes an engagement, or needs to know what LordGen
actually sells.

**Status: stub.** The eight candidate offers below come straight from the brief. None of the
fields under them are filled in yet — no pricing, no confirmed ICP, no real scope. A skill
built against blanks should say so and ask, not invent plausible-sounding numbers. Fill each
offer in from real client conversations as they happen, one at a time, rather than guessing
all eight up front.

Every offer needs: ideal customer, pain, outcome, scope, timeline, inputs, deliverables,
price anchor, optional recurring add-on, success metric, case-study format.

## Candidate offers

1. **AI Operations Audit**
2. **Lead Response Automation**
3. **Sales/CRM Automation**
4. **AI Knowledge Assistant**
5. **Proposal/Outreach Engine**
6. **Internal AI Agent System**
7. **AI Workflow Modernization**
8. **AI Governance & Reliability Package**

```yaml
# Template — copy per offer once it's real
offer: <name>
ideal_customer: <who, specifically — industry, size, role bought from>
pain: <the problem they'd recognize themselves in>
outcome: <what changes for them after delivery>
scope: <what's included, explicitly — and what's not>
timeline: <weeks to first delivery>
inputs: <what LordGen needs from the client to start>
deliverables: <concrete artifacts handed over>
price_anchor: <starting price or range>
recurring_addon: <optional retainer built around ongoing value — monitoring, optimization,
  new workflows, model upgrades, regression testing, cost review, security review, reporting —
  not repackaged "support hours">
success_metric: <how "it worked" gets measured>
case_study_format: <what gets sanitized and reused as proof>
```

## Recurring revenue and reusable IP

Retainers should be built around ongoing value (monitoring, optimization, new workflows,
model upgrades, regression testing, cost review, security review, reporting), never "support
hours" repackaged. Every project should drop its sanitized reusable pieces into `templates/`
and `prompts/` once those folders exist in a working repo — that's the IP factory, not a
separate system.

## Changelog
- v1.0 — Initial stub, offer names and required fields from `LordGen_Master_Brief_v3.md` §3
