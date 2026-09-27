# lordgen-skill-builder

A Claude Code skill for designing, writing and testing other skills, plus the
business reference set those skills are written against. For anyone building a
small library of agent skills who wants them to trigger reliably and stay honest
about what they may automate.

Built by **[Lordmark Dorgu](https://github.com/Kikobazz123)** · MIT licensed.

---

## The problem it solves

Agent skills fail in three predictable ways: they never fire because the description
reads like a label rather than a trigger, they fire on everything and waste context,
or they fire and perform no better than no skill at all. A second problem sits
underneath: a skill written without knowing what the business sells, which tool owns
what, and which actions need a human will guess, and a guess that sends an email is
expensive. This repo addresses both: a method for building skills, and one place to
read the facts they depend on.

## Stack

Markdown and YAML frontmatter, in the Claude Code `SKILL.md` format. No runtime code.

## Architecture

```
skills/skill-builder/
  SKILL.md                 the method: decision gate, trigger writing, progressive
                           disclosure, testing, definition of done
  references/anatomy.md    how a skill package is laid out
  references/testing.md    baseline-vs-skill runs, assertions, trigger evals
  assets/SKILL-template.md starting point for a new skill
references/
  README.md                which file to read for which kind of skill
  architecture.md          mission, the three business loops, layer map
  platform-map.md          which connected tool owns which job
  human-in-the-loop.md     Green / Yellow / Red policy for automation
  offers.md                offer catalogue (names only; fields left blank)
  brand.md                 pointer to the brand guideline and logo files
```

A new skill starts at Step 0 of `SKILL.md`, reads the matching file in
`references/`, and is tested against a no-skill baseline before it counts as done.

## Run locally

Copy `skills/skill-builder/` into `~/.claude/skills/` (user-wide) or a project's
`.claude/skills/`, then ask Claude Code to make or fix a skill. The references are
read on demand; keep them next to the skill or point to them from the new skill.

## Tests

No automated tests in this repo. `references/testing.md` describes the evaluation
method the skill applies to the skills it builds (run each test prompt with and
without the skill, check assertions, run trigger evals on should-fire and
should-not-fire prompts); no eval runs are committed here.

## Design decisions and trade-offs

- **Decide the surface before writing a skill.** A rule that must always hold belongs
  in a hook or permission, not prose; always-on context belongs in a short
  `CLAUDE.md`; deterministic work belongs in a script the skill calls. Most requests
  to "make a skill" are better served elsewhere, and the skill says so.
- **Baseline first.** If a capable model already does the task well without the
  skill, the skill only adds context cost. Testing against no-skill output is
  mandatory, not optional.
- **AI reasons, software executes, humans approve.** `human-in-the-loop.md` sorts
  actions into Green (automate: research, drafting, internal reports), Yellow
  (prepare, then stop for approval: client messages, proposals, publishing) and Red
  (human only: money, contracts, destructive or irreversible actions). Skills that
  draft outreach stop at a reviewed draft.
- **Blank rather than invented.** The offer catalogue lists offer names with every
  pricing and scope field empty; filling them with plausible numbers would teach
  every downstream skill to quote prices nobody agreed to.
- **Kept separate from the projects that use it.** Related work lives in its own
  repos, such as [lordgen-scraper](https://github.com/Kikobazz123/lordgen-scraper) and
  [lordgen-newsletter-pipeline](https://github.com/Kikobazz123/lordgen-newsletter-pipeline);
  merging them is a deliberate later step, not an accident of layout.

The references are condensed from a private master brief (v3), which cut an earlier,
larger system design down to what one founder can run. `brand.md` points to the brand
guideline and logo files in
[lordgen-newsletter-pipeline](https://github.com/Kikobazz123/lordgen-newsletter-pipeline).
