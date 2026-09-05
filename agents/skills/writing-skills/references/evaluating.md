# Evaluating output quality

Once a skill triggers, check whether it actually produces good output, reliably
and better than no skill. This is the eval-driven loop from
https://agentskills.io/skill-creation/evaluating-skills.

## Design test cases

A test case has three parts:

- Prompt: a realistic message someone would actually type.
- Expected output: a human-readable description of success.
- Input files (optional): files the skill works with.

Store them in `evals/evals.json` in the skill directory:

```json
{
  "skill_name": "csv-analyzer",
  "evals": [
    {
      "id": 1,
      "prompt": "I have a CSV of monthly sales in data/sales_2025.csv. Find the top 3 months by revenue and make a bar chart.",
      "expected_output": "A bar chart showing the top 3 months by revenue, with labeled axes and values.",
      "files": ["evals/files/sales_2025.csv"]
    }
  ]
}
```

Tips:

- Start with 2-3 cases. Don't over-invest before the first results.
- Vary phrasing, detail, and formality. Some casual, some precise.
- Cover at least one edge case: malformed input, an unusual request, or an
  ambiguous instruction.
- Use realistic context (file paths, column names). "Process this data" is too
  vague to test anything.

Hold off on pass/fail checks until you've seen the first outputs.

## Run each case with and without the skill

Run every test twice: once with the skill, once without (or against the previous
version). The baseline tells you what the skill actually adds.

Organize results in a workspace next to the skill, one directory per iteration:

```
csv-analyzer/
├── SKILL.md
└── evals/evals.json
csv-analyzer-workspace/
└── iteration-1/
    ├── eval-top-months-chart/
    │   ├── with_skill/    (outputs/, timing.json, grading.json)
    │   └── without_skill/ (outputs/, timing.json, grading.json)
    └── benchmark.json
```

You author `evals/evals.json` by hand. The other JSON files are produced during
the run.

Give each run a clean context so the agent follows only the `SKILL.md`, not
leftover state. Subagents give this isolation for free; without them, use a
fresh session per run. For each run provide the skill path (or none for the
baseline), the prompt, input files, and an output directory. When improving an
existing skill, snapshot the old version and use it as the baseline.

Record timing per run so you can weigh cost against quality:

```json
{ "total_tokens": 84852, "duration_ms": 23332 }
```

## Write assertions

Assertions are verifiable statements about the output. Add them after seeing the
first outputs; you often don't know what "good" means until the skill has run.

Good: "The output file is valid JSON", "The bar chart has labeled axes", "The
report includes at least 3 recommendations". Verifiable, specific, countable.

Weak: "The output is good" (too vague), "uses exactly the phrase 'Total Revenue:
$X'" (too brittle). Not everything needs an assertion; style and "feels right"
belong in human review.

```json
"assertions": [
  "The output includes a bar chart image file",
  "The chart shows exactly 3 months",
  "Both axes are labeled",
  "The chart title or caption mentions revenue"
]
```

## Grade

Score each assertion PASS or FAIL with concrete evidence that quotes or points
at the output. Use a script for mechanical checks (valid JSON, row counts, file
exists) and an LLM for judgment calls.

```json
{
  "assertion_results": [
    { "text": "Both axes are labeled", "passed": false,
      "evidence": "Y-axis labeled 'Revenue ($)' but X-axis has no label" }
  ],
  "summary": { "passed": 3, "failed": 1, "total": 4, "pass_rate": 0.75 }
}
```

Require real evidence for a PASS; don't give benefit of the doubt. A section
titled "Summary" with one vague sentence is a FAIL. While grading, also fix
assertions that are too easy, too hard, or unverifiable. For comparing two
versions, try a blind comparison: show both outputs to an LLM judge without
saying which is which and have it score overall quality.

## Aggregate

Compute per-configuration stats in `benchmark.json`:

```json
{
  "run_summary": {
    "with_skill":    { "pass_rate": { "mean": 0.83 }, "tokens": { "mean": 3800 } },
    "without_skill": { "pass_rate": { "mean": 0.33 }, "tokens": { "mean": 2100 } },
    "delta":         { "pass_rate": 0.50, "tokens": 1700 }
  }
}
```

The delta shows what the skill costs (time, tokens) and what it buys (pass rate).
Adding 13 seconds for a 50-point jump is likely worth it; doubling tokens for 2
points is not. Standard deviation only means something with multiple runs per
eval, so early on focus on raw pass counts and the delta.

## Analyze patterns

Averages hide things. After aggregating:

- Remove assertions that always pass in both configs. They inflate the score
  without reflecting skill value.
- Investigate assertions that always fail in both. The assertion may be broken,
  the case too hard, or it's checking the wrong thing.
- Study assertions that pass with the skill but fail without. That's where the
  skill adds value; understand why.
- Tighten instructions when results swing run to run (high stddev). The eval may
  be flaky, or the instructions ambiguous. Add examples or specifics.
- Read the transcript for time and token outliers to find the bottleneck.

## Review with a human

Assertions only check what you thought to write. A human catches the rest:
output that's technically correct but misses the point, or problems hard to
express as pass/fail. Record specific, actionable feedback per case (empty means
it looked fine):

```json
{
  "eval-top-months-chart": "Chart is missing axis labels and months are in alphabetical, not chronological, order.",
  "eval-clean-missing-emails": ""
}
```

## Iterate

You now have three signals: failed assertions (specific gaps), human feedback
(broader quality), and transcripts (why things went wrong). Give all three plus
the current `SKILL.md` to an LLM and ask for changes. Guide it to:

- Generalize from feedback. Fix the underlying issue broadly, don't patch the
  specific test case.
- Keep the skill lean. Remove instructions that transcripts show cause wasted
  work. If pass rates plateau while you add rules, it may be over-constrained;
  try removing some.
- Explain the why. Write "Do X because Y causes Z" instead of "ALWAYS do X,
  NEVER do Y."
- Bundle repeated work. If every run rebuilds a similar helper, put it in
  `scripts/`. See [scripts.md](scripts.md).

The loop: propose changes, apply them, rerun all cases in a new `iteration-<N+1>`
directory, grade and aggregate, review with a human, repeat. Stop when you're
satisfied, feedback is consistently empty, or improvement stalls.
