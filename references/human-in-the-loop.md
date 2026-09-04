---
name: lordgen-human-in-the-loop
description: Which LordGen actions may run unattended vs. require human approval. Read before building any skill that drafts outreach, proposals, pricing, or anything published externally.
---

# Human-in-the-loop policy

Read when: a skill's output could plausibly leave LordGen's internal workspace, cost money,
or bind the business to something.

**Green — safe to automate:** research, drafting, internal classification, formatting, data
normalization, internal reports, test execution.

**Yellow — automate the prep, require approval before the action itself:** client
communications, marketplace proposals, pricing changes, publishing, outreach, contract-related
messages, external posts.

**Red — explicit human authorization only:** financial transactions, contracts, legal
commitments, destructive actions, credential changes, production access changes, client data
deletion, irreversible external actions.

## What this means for a skill

- A skill is free to draft a proposal, a sequence, or a post (Green) all the way to a
  finished artifact.
- A skill must **stop short of sending it** if the action is Yellow — hand off a
  ready-to-review draft and say explicitly that it's waiting on approval, rather than
  submitting, sending, or publishing on its own.
- A skill should **never attempt** a Red action at all, not even with a "confirm?" prompt
  baked in — those require a human acting directly, not a human approving an agent's proposed
  action.
- If a task is ambiguous between Yellow and Red, treat it as Red until told otherwise —
  the cost of an unwanted send or commitment is much higher than the cost of asking first.

## Two concrete cases from the brief

**Upwork:** no official proposal-submission API exists. The lean pipeline is: opportunity
found → Claude drafts a fit score + proposal draft → human reviews and edits → human submits
manually. Do not attempt browser automation to bypass platform controls — that risks the
account entirely, independent of the Green/Yellow/Red framing above.

**Direct outreach (Apollo.io):** prospecting and drafting are Green/Yellow; sending outreach
at scale, changing pricing, and making capability representations are all Yellow-or-above and
need sign-off before they go out.

## Changelog
- v1.0 — Initial, condensed from `LordGen_Master_Brief_v3.md` §5, §13
