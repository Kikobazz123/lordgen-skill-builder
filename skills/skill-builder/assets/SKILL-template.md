---
name: <lowercase-hyphenated-matches-directory>
description: <What it does, one clause>. Use when <2-4 concrete situations>, including when the user says <realistic phrasings> — or <oblique case where they never name the domain>.
---

# <Skill Name>

## Goal

<One paragraph: what this makes possible that's currently inconsistent, and why that matters. Not a restatement of the description.>

<Optional: a table of the specific failure modes this skill exists to prevent. Naming them shapes every decision below.>

---

## <Decision gate — delete if the skill always applies>

<Either: when NOT to proceed, and what to do instead.
Or: which variant applies, pointing to the right references/ file.
This section exists to prevent confident work on the wrong thing.>

---

## Process

### 1. <Imperative step name>

<What to do. State the success condition so the reader knows when to move on.>

### 2. <Imperative step name>

<Where a step has a reason that isn't obvious, give it — a reader who understands why generalizes correctly to cases not listed here.>

### 3. <Imperative step name>

<...>

---

## Output contract

<Show it. A literal template or an input→output pair transfers format far more reliably than describing it.>

```
<template or worked example>
```

---

## Rules

- **<Rule>** — <why it exists. A rule without a reason gets applied literally and wrongly at the first edge case.>
- **<Rule>** — <reason>
- **<Safety or irreversibility line>** — <these are the ones that can be emphatic without explanation>

---

## Definition of done

- [ ] <Objectively checkable condition>
- [ ] <Objectively checkable condition>
- [ ] <Condition covering the most likely failure mode>

---

## Reference map

<Delete if there are no references. Otherwise name each file and the condition that should send a reader to it — a reference nobody is told to read is dead weight.>

- `references/<file>.md` — <what's in it, and when to read it>
- `scripts/<file>.py` — <what it does; call it rather than re-deriving the logic>
- `assets/<file>` — <what it's for>

---

## Self-improvement

<Keep. Signals specific to this skill: what a miss looks like, what a false positive looks like, what work keeps getting redone inline and should become a script.>

## Changelog
- v1.0 — Initial
