# Anatomy Reference

Read when: setting up a new skill package, deciding what goes in frontmatter, calculating token budgets, or targeting a platform other than Claude Code.

**Contents**
1. Frontmatter fields
2. Directory conventions and install locations
3. Invocation: automatic vs. manual
4. Token accounting
5. Portability across platforms
6. Splitting an overgrown file

---

## 1. Frontmatter fields

Two fields are required. Everything else is optional, varies by platform and version, and should be verified against current documentation before you depend on it.

```yaml
---
name: skill-name          # required — lowercase, hyphenated, matches directory
description: <what it does + when to use it>   # required — the trigger
---
```

**`name`** — matches the containing directory. This is also the manual-invocation handle (`/skill-name`), so keep it short and typeable.

**`description`** — the entire trigger mechanism. Claude reads this and nothing else when deciding whether to load the body. Budget roughly 100 tokens; spend them on trigger situations rather than elegant prose.

**Optional fields seen in the wild** — tool restrictions (limiting which tools the skill may use), model hints, license, and metadata blocks. Support is inconsistent across platforms and versions. Two consequences worth taking seriously:

- Don't invent a field name from memory. A misspelled or unsupported key is silently ignored, which reads as "the skill is broken" with no error to debug.
- A skill using only `name` and `description` runs everywhere. Each optional field narrows portability.

If a tool restriction genuinely matters — a skill that should never touch the network, say — check the current spec, then verify the restriction actually applies by testing it, not by trusting the frontmatter.

---

## 2. Directory conventions and install locations

```
skill-name/
├── SKILL.md              # required
├── references/           # markdown docs, loaded only when the body says to
├── scripts/              # executables — run without entering context
└── assets/               # templates, fixtures, fonts, files used in output
```

Nothing outside `SKILL.md` loads automatically. That's the design, not a gap: it's what makes depth free until it's needed.

**Typical locations in Claude Code:**
- Project scope: `.claude/skills/<name>/SKILL.md` — travels with the repo, shared with the team via git
- Personal scope: `~/.claude/skills/<name>/SKILL.md` — available across all your projects
- Plugin-provided and built-in skills are also discovered automatically

Exact paths have shifted between versions. If a skill isn't being discovered, verify the current expected path rather than assuming the file is malformed.

**Project vs. personal is a real decision.** Project scope for anything encoding how *this* codebase works — its conventions, its deploy path, its data model. Personal scope for how *you* work regardless of repo. Putting a personal preference in project scope imposes it on everyone who clones.

---

## 3. Invocation: automatic vs. manual

**Automatic** — Claude matches the request against available descriptions and loads what fits. This is the default and where description quality pays off.

**Manual** — `/skill-name` loads it explicitly.

Manual invocation is the right default for skills with side effects. A deploy skill, a send skill, or anything that publishes shouldn't fire on inference; make the body say so, so the operator knows it's expected to be called deliberately rather than discovered.

---

## 4. Token accounting

Rough per-skill cost:

| Tier | When charged | Typical |
|---|---|---|
| Frontmatter | Every session, whether or not the skill is used | ~60–100 tokens |
| Body | On trigger | Aim under ~5k tokens (~500 lines) |
| References | Only when explicitly read | Whatever you read |
| Scripts | Never, if executed rather than read | ~0 |

Two implications that change how you write:

**Frontmatter cost is multiplied by skill count.** Ten skills is a fixed tax on every session before anyone types anything. That's the argument against near-duplicate skills — the cost is paid always, the benefit only sometimes.

**Body cost is paid per trigger, and compaction re-injects skills under a cap** (both per-skill and total across skills). A body that overruns risks truncation in exactly the long sessions where it's most needed. Treat the ~500-line guidance as a real ceiling, not a style note.

**Scripts are the cheapest tier by a wide margin.** Prose instructing the model to perform a deterministic transformation costs tokens on every trigger *and* every run, and can be executed wrong. A script costs its invocation. Anything with a fixed algorithm belongs in `scripts/`.

---

## 5. Portability across platforms

The SKILL.md format was released as an open standard and has been adopted beyond Claude — several coding agents and IDE assistants now read the same structure.

What this means practically:

- The two-required-field baseline (`name`, `description`) plus a markdown body is the portable core. Skills written to it move between platforms with no changes.
- Platform-specific frontmatter, hard-coded absolute paths, and assumptions about which tools exist are what break portability.
- Where a skill genuinely needs platform-specific behavior, branch inside the body — a short "if the platform provides X, do Y; otherwise Z" — rather than forking the file. Two copies drift.

Adoption details change quickly. Verify current support before promising a skill runs somewhere specific.

---

## 6. Splitting an overgrown file

When a body crosses ~500 lines, split by **when the content is needed**, not by topic tidiness:

1. **Keep in the body:** the goal, the decision gate, the main process, hard rules, the definition of done. Anything needed on *every* invocation.
2. **Move to `references/`:** per-variant detail, edge-case catalogues, format specs, long examples. Anything needed on *some* invocations.
3. **Move to `scripts/`:** anything deterministic.
4. **Leave explicit pointers.** A reference nobody is told to read is dead weight — name the file and the condition that should send the reader to it.

The same logic applies to an overgrown `CLAUDE.md`, which is the more common version of this problem. Always-on project context stays in root `CLAUDE.md` because compaction preserves it; task-specific knowledge becomes a skill; must-always-hold rules become hooks or permissions. Splitting a `CLAUDE.md` into nested files is the tempting move and the wrong one — compaction drops nested and path-scoped files until they're re-read, so a rule moved there can quietly stop applying mid-session.
