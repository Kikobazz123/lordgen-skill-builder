# Testing Reference

Read when: running test cases for a skill, designing assertions, building a trigger eval set, or comparing two versions.

**Contents**
1. Why the baseline is mandatory
2. Workspace layout
3. Writing test prompts
4. Assertions
5. Trigger evals
6. The iteration loop
7. Adapting when subagents aren't available

---

## 1. Why the baseline is mandatory

Running a skill and liking the output proves nothing. Capable models produce good output on many tasks unaided — so without a same-prompt no-skill run, you can't distinguish "the skill worked" from "the task was easy."

Run both, always:

- **New skill** → baseline is no skill at all.
- **Existing skill being improved** → baseline is the previous version. Snapshot it before editing (`cp -r <skill> <workspace>/skill-snapshot/`), then point the baseline at the snapshot.

Launch both in the same pass rather than doing with-skill runs first and baselines later — same conditions, and they finish together.

Compare on three axes, not one:

| Axis | Question |
|---|---|
| Quality | Is the output actually better, by the user's standard? |
| Cost | Tokens and steps consumed — a marginal quality gain at triple the cost is a bad trade |
| Consistency | Does it hold across all test prompts, or just the one it was built from? |

---

## 2. Workspace layout

Keep results beside the skill, never inside it — test artifacts in a shipped skill are noise:

```
<skill-name>-workspace/
├── skill-snapshot/           # pre-edit copy, when improving
├── iteration-1/
│   ├── <descriptive-eval-name>/
│   │   ├── with_skill/outputs/
│   │   ├── without_skill/outputs/    (or old_skill/outputs/)
│   │   └── eval_metadata.json
│   └── ...
└── iteration-2/
```

Name eval directories for what they test (`malformed-headers`, `empty-input`), not `eval-0`. When you're comparing three iterations, descriptive names are the difference between reading results and re-deriving what each one was for. Create directories as you go rather than scaffolding everything upfront.

---

## 3. Writing test prompts

Two or three is enough to start. What matters is that they're *realistic* — the failure mode is testing against prompts only the skill's author would write.

Realistic means: concrete file paths, plausible names and values, actual context about why the user wants it, and at least one written casually — lowercase, abbreviated, maybe a typo. Vary the length.

Weak: `Format this data`
Strong: `got handed a csv from our billing export (downloads folder, "invoices_jan_FINAL(2).csv") and half the rows have the header repeated in the middle. need it clean enough to pivot on`

Cover deliberately:
- The straightforward central case
- One awkward or partial input
- One case at the edge of scope — the answer might legitimately be "this skill declines and explains why"

**Show the prompts to the user before running them.** Bad test cases produce confident wrong conclusions, and the user spots unrealistic ones immediately.

---

## 4. Assertions

Assertions are for objectively checkable properties. Force them onto subjective qualities and you get a passing score that means nothing.

**Assertable:** file exists at path; output is valid JSON; every input row appears in output; no hardcoded credentials; heading structure matches the template.

**Not assertable — use human review:** tone, design quality, whether the summary captured the important part, whether the code is idiomatic.

Write assertions as descriptive statements, so results are readable at a glance:

```json
{
  "eval_name": "malformed-headers",
  "prompt": "<the prompt>",
  "assertions": [
    "Output CSV has exactly one header row",
    "All 412 data rows from the input are present",
    "Numeric columns contain no currency symbols"
  ]
}
```

**Check programmatically wherever possible.** A script that verifies row counts is faster and more reliable than reading output and forming an impression, and it's reusable across every later iteration. Eyeballing is for the assertions no script can settle.

---

## 5. Trigger evals

Separate exercise from output testing, and often the higher-value one — a skill that never fires can't produce bad output.

Build 16–20 prompts, split roughly evenly between should-fire and should-not-fire.

**Should-fire (8–10)** — cover phrasing variety, not just intent variety:
- Formal and casual versions of the same request
- Cases where the user never names the domain or file type but clearly needs the skill
- An uncommon but legitimate use
- A case where this skill competes with another and should win

**Should-not-fire (8–10)** — these carry most of the diagnostic value, and only if they're hard:
- Adjacent domains sharing vocabulary
- Phrasings a naive keyword match would catch
- Requests touching what the skill does, in a context where another tool is correct

The mistake that wastes the whole exercise is making negatives obviously irrelevant. "Write a fibonacci function" as a negative for a PDF skill tests nothing. A good negative is one you have to think about.

```json
[
  {"query": "<realistic prompt>", "should_trigger": true},
  {"query": "<near-miss prompt>", "should_trigger": false}
]
```

Run each query more than once — triggering isn't fully deterministic, so a single pass gives a noisy rate. When tuning the description against results, hold some queries back rather than tuning against all of them; a description tuned on everything looks better than it is.

---

## 6. The iteration loop

1. Run all test cases, with-skill and baseline, into `iteration-N/`.
2. Grade the assertions; script what's scriptable.
3. Put outputs in front of the user before forming your own conclusions — they catch things assertions can't, and their read should shape the revision rather than confirm it.
4. Revise the skill.
5. Rerun everything into `iteration-N+1/`, baseline included.

Stop when the user is satisfied, feedback comes back empty, or iterations stop producing meaningful change.

**When revising, four habits separate real improvement from thrashing:**

- **Generalize from the feedback.** A fix that satisfies exactly the failing example usually overfits. Ask what class of input that example represents.
- **Read the transcripts, not just outputs.** If the model wasted ten steps before getting there, the skill may be sending it somewhere unproductive — and the fix is often deleting a section, not adding one.
- **Notice repeated work across cases.** If every run independently wrote a similar helper script, bundle that script. Write once instead of paying per invocation.
- **Cut as much as you add.** A skill grows monotonically unless someone removes what stopped earning its place.

---

## 7. Adapting when subagents aren't available

Parallel isolated runs aren't always possible. The loop still works, with two honest caveats.

Read the skill, then follow its instructions yourself for each test prompt, one at a time. Skip parallelism and skip formal baseline comparison — you wrote the skill and you're executing it, so you carry context a fresh run wouldn't, and any measured advantage is contaminated.

That makes this a sanity check rather than a benchmark. Two things compensate:

- **Trigger evals stay valid.** They test description matching, which doesn't depend on isolated execution — and they're the higher-value test anyway.
- **Human review carries more weight.** Show the user each prompt and its output directly. For file outputs, save them and say where, so they can open and inspect rather than trust a summary.

Say which mode you ran in when reporting results. "Tested and it works" means something different in each, and the difference matters to whoever relies on the skill later.
