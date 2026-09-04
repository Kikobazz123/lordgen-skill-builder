---
name: skill-builder
description: Designs, writes, tests, and improves Claude Code skills (SKILL.md packages) to a production quality bar. Use this whenever the user wants to create a new skill, turn a repeated workflow or prompt into something reusable, review or fix a skill that triggers unreliably, split an overgrown SKILL.md or CLAUDE.md, or decide whether a piece of guidance belongs in a skill, a hook, a subagent, or CLAUDE.md at all — including when they say "make this repeatable", "capture how I do this", "why isn't Claude using my skill", or describe doing the same multi-step task a third time without using the word "skill".
---

# Skill Builder

## Goal

Produce skills that stay useful after the conversation that created them — reliable in triggering, cheap in context, and general enough to work on inputs nobody anticipated.

Three failure modes account for most bad skills. Design against all three:

| Failure | What it looks like | Root cause |
|---|---|---|
| **Never fires** | Skill exists, Claude ignores it, user does the work manually | Description written as a label, not a trigger |
| **Fires on everything** | Loads during unrelated work, wastes context, derails tasks | Description too broad, no near-miss discipline |
| **Fires and underperforms** | Triggers correctly, output no better than no skill | Overfitted to the examples it was built from |

A skill that only works on the three prompts it was tested against has negative value: it costs context on every session and delivers nothing new.

---

## Step 0 — Decision gate: should this be a skill at all?

Run this before writing anything. Most requests to "make a skill" are better served by a different surface, and building the wrong one is the most expensive mistake available here — a rule living in prose is a rule that gets skipped the first time context gets tight.

| The guidance is… | Correct home | Why |
|---|---|---|
| A rule that must **always** hold (no secrets in commits, never touch prod) | Hook or permission setting | Enforcement beats instruction. Prose is advisory; a hook is not |
| Reference knowledge needed only for **specific** tasks | **Skill** ✅ | Loads on demand, costs nothing when idle |
| A bounded side-task that would flood the main thread with reads | Subagent | Own context window; keeps main session focused |
| Always-on context **every** session needs | Root `CLAUDE.md`, kept short | Survives compaction; nested files don't |
| A one-off transformation | Just do the task | A skill is overhead if it runs once |
| Deterministic, repeatable computation | A script the skill calls | Code doesn't hallucinate. Don't ask a model to do arithmetic a function does |

Two more gates worth applying honestly:

- **Would a competent model do this well without the skill?** If yes, the skill adds context cost and nothing else. Test the baseline before you build.
- **Does something similar already exist?** Extending a skill beats adding a near-duplicate. Two skills with overlapping descriptions compete for the same trigger and both get less reliable.

State the recommendation plainly when the answer is "not a skill." Building one anyway to be agreeable produces a file nobody benefits from.

---

## Step 1 — Capture intent

Mine the conversation before asking questions. If the user has been doing the workflow in-thread, the tool sequence, the corrections they made, and the output format they kept steering toward are already on record — extracting those and confirming beats an interrogation.

Establish four things:

1. **Capability** — what should Claude be able to do that it currently does inconsistently?
2. **Trigger surface** — what would a user actually type? Collect real phrasings, including casual and oblique ones.
3. **Output contract** — exact shape of a correct result. Vague here means unverifiable later.
4. **Verifiability** — objective outputs (file transforms, extraction, code generation, fixed sequences) support test assertions. Subjective outputs (tone, design taste) need human review instead. Say which this is; don't force assertions onto judgment calls.

Then ask about the parts users routinely forget: edge cases, malformed inputs, what "done" means, and what the skill should explicitly *not* do.

---

## Step 2 — Write the trigger

The `description` field is the only thing Claude sees when deciding whether to load the skill. Everything about when to use it lives here — not in the body, which loads too late to affect the decision.

Two opposing pressures, and both matter:

**Undertriggering is the more common failure.** Claude tends not to consult skills for tasks it thinks it can handle alone, so a purely factual description gets skipped. Counter it by naming concrete situations and phrasings, including ones where the user won't name the skill or its domain.

**Overtriggering wastes context on every false positive.** Counter it by scoping the domain explicitly and naming what's adjacent-but-out-of-scope.

Structure that satisfies both:

```
<what it does, one clause>. Use when <2-4 concrete situations>,
including when the user says <realistic phrasings> — or <oblique case
where they never name the domain>.
```

Weak: `Helps with spreadsheet formatting.`
Strong: `Cleans and reformats messy tabular data into analysis-ready spreadsheets. Use when the user has a CSV or xlsx with malformed rows, headers in the wrong place, or mixed types — including when they just say "this file is a mess" or paste a broken table without naming the format.`

The second names the artifacts, the symptoms, and a phrasing that omits the domain entirely. That third element is what closes the undertrigger gap.

---

## Step 3 — Structure for progressive disclosure

Skills load in three tiers. Design deliberately for each, because the cost profile differs sharply.

| Tier | Loads | Budget | Holds |
|---|---|---|---|
| Frontmatter | Every session, always | ~100 tokens | `name`, `description` |
| SKILL.md body | On trigger | Under ~500 lines / ~5k tokens | Process, rules, decision logic |
| `references/`, `scripts/`, `assets/` | Only when the body says to read them | Effectively unbounded | Depth, edge cases, executables, templates |

```
skill-name/
├── SKILL.md              # required — frontmatter + body
├── references/           # docs read on demand
├── scripts/              # executables; run without loading into context
└── assets/               # templates, fixtures, files used in output
```

The tier-3 distinction earns its keep: a script *executes* without its source entering context. Anything deterministic and repeated belongs there, not as prose instructions the model re-derives every run.

**Budget discipline that isn't arbitrary.** Compaction re-injects skills under a per-skill and total cap; a body that overruns it can get truncated exactly when a long session most needs it. Past ~500 lines, split into `references/` with explicit pointers rather than letting one file absorb everything. For any reference over ~300 lines, open it with a table of contents so the reader can stop early.

**Organize by variant when a skill spans several.** One body holding the shared workflow plus `references/<variant>.md` per case means only the relevant variant loads. A single file covering all of them pays for all of them, every time.

---

## Step 4 — Write the body

**Imperative, and explain why.** Stacked ALL-CAPS MUSTs are a yellow flag: they signal the author couldn't explain the reason. A model that understands *why* a constraint exists applies it correctly to cases the author never listed. A model following a bare directive fails the moment reality differs from the example. Reserve emphatic phrasing for genuine safety and irreversibility.

**Show the output contract, don't describe it.** A literal template or an input→output example pair transfers format more reliably than a paragraph about format.

**Write for the general case.** You'll build from two or three examples; the skill will run on hundreds. When tempted to add a rule that only makes sense for the example in front of you, generalize it or drop it.

**Include the negative space.** What the skill should refuse, escalate, or leave alone is often more valuable than the happy path, and it's what stops confident wrong output.

**Suggested body order** — front-load what's needed most, since a reader may stop early:

1. Goal — one paragraph, why this exists
2. Decision gate — when not to proceed, or which variant applies
3. Process — numbered, each step with its own success condition
4. Output contract — template or worked example
5. Rules — hard constraints, with reasons
6. Reference map — what's in `references/`, and when to read each
7. Definition of done — the checklist below
8. Self-improvement — the log below

---

## Step 5 — Test before declaring it works

Write 2–3 test prompts a real user would plausibly type — specific, with file paths, real-sounding names, some casual phrasing. Show them to the user before running: bad test cases produce confidently wrong conclusions.

Then run each **twice — once with the skill, once without.** The baseline is not optional. Without it there's no way to know whether the skill helped or the model was simply capable. Compare on output quality, token cost, and step count.

For trigger reliability, build a set of roughly 8–10 should-fire and 8–10 should-not-fire prompts. The negatives carry most of the signal, so make them genuine near-misses — same vocabulary, different actual need. An obviously unrelated negative tests nothing.

Full protocol, including assertion design and the iteration workspace layout: `references/testing.md`.

---

## Rules

- **Never fabricate frontmatter fields.** `name` and `description` are the required pair; other fields (tool restrictions, model hints) vary by platform and version. Verify against current docs before relying on one, and prefer the two-field baseline for portability.
- **No secrets, no credentials, no live endpoints** in a skill or its assets. Skills get committed, shared, and packaged.
- **No malicious or deceptive capability**, and no skill whose actual behavior would surprise a user reading its description.
- **Consequential actions stay gated.** A skill that sends, publishes, deploys, pays, or deletes describes the action and hands off for approval — it doesn't perform it unattended.
- **Preserve identity when updating.** Keep the existing directory name and `name` field; don't create `-v2`. Copy read-only installed skills to a writable path before editing.
- **One skill, one job.** If the description needs "and also", it's two skills.
- **Efficiency never overrides substance.** Cut redundancy — the same instruction restated three ways — not content. Safety rules and approval gates stay as long as they need to be.

---

## Definition of done

A skill ships when all of these hold. Anything unchecked is unfinished work, not a nice-to-have:

- [ ] Description names both what it does and 2+ concrete trigger situations, including one oblique phrasing
- [ ] Passed the Step 0 gate — a skill is genuinely the right surface
- [ ] Body under ~500 lines; overflow lives in `references/` with explicit read-pointers
- [ ] Output contract shown as template or worked example, not described
- [ ] Every rule states its reason, or is genuinely a safety line
- [ ] Tested against baseline on 2+ realistic prompts, and beat it
- [ ] Trigger set run: near-miss negatives don't fire it
- [ ] Nothing overfitted to the examples it was built from
- [ ] No secrets; consequential actions gated
- [ ] Version and changelog present

---

## Self-improvement

Skills decay — tools change, projects move, the original examples stop being representative. Build the maintenance path in from the start rather than bolting it on.

Keep a short log at the bottom of every skill:

```markdown
## Changelog
- v1.2 — Added negative case for X; description was firing on adjacent Y
- v1.1 — Extracted the parsing steps into scripts/parse.py (was re-derived every run)
- v1.0 — Initial
```

Four signals, and the specific move each one calls for:

| Signal | Move |
|---|---|
| Skill didn't fire when it should have | Add that exact phrasing to the description. Real misses are the best trigger data available |
| Fired when it shouldn't have | Tighten the domain, name the adjacent case as out-of-scope |
| Same helper script written inline across runs | Promote it to `scripts/`. Write once, stop paying per invocation |
| A section never influences output | Delete it. Unused instructions cost context every trigger and dilute the rest |

**Prune on the same pass as you add.** Skills grow monotonically unless someone actively removes what stopped pulling its weight, and a bloated skill degrades before it hits any hard limit.

**Re-baseline after material edits.** An improvement that wasn't measured against the previous version is a guess. Keep the prior version as the comparison point.

---

## Reference map

Read these only when the current task needs them — that's the whole point of the tier split:

- `references/anatomy.md` — frontmatter fields, directory conventions, install locations, token accounting, portability across platforms implementing the Agent Skills standard
- `references/testing.md` — eval workspace layout, assertion design, trigger-eval construction, baseline comparison protocol
- `assets/SKILL-template.md` — fill-in scaffold with every section stubbed; copy this to start a new skill

---

## Project profile (optional, per-repo)

For repo-specific conventions, keep a short block here rather than duplicating the whole standard in each skill. Everything above stays project-agnostic and portable.

**This repo — LordGen AI Skill builder:**
- Skills live at `skills/<name>/SKILL.md`, this one included — same conventions it documents
- Business and brand grounding for any new LordGen skill lives in `../../references/` (offers, brand identity, architecture, human-in-the-loop policy) — read the relevant file there before writing a skill that touches outreach, proposals, pricing, or brand-facing output, rather than restating that content inline
- Any skill touching outreach, publishing, pricing, or client communication is preparation-only per `../../references/human-in-the-loop.md` — the approval gate is non-negotiable
- Cap files at 150–300 lines per the project documentation standard, then split

---

## Changelog
- v1.1 — Relocated into `skills/skill-builder/`; project profile points at the real `references/` folder instead of a hypothetical example
- v1.0 — Initial
