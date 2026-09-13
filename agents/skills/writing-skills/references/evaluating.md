# Evaluating output quality

Structured evals answer whether a skill works reliably across varied prompts, in edge cases, and against a run with no skill at all.

## Test cases

A test case is a realistic prompt, a human-readable description of what success looks like, and optional input files. Store them in `evals/evals.json` inside the skill directory.

- Start with 2 to 3 cases. Don't over-invest before the first results.
- Vary phrasing, detail, and formality.
- Cover at least one boundary condition, like malformed input or an ambiguous request.
- Use realistic context: file paths, column names, personal details. "Process this data" tests nothing.
- Don't write pass/fail checks yet.

## Running

Run each case twice, once with the skill and once without it or with the previous version, for a baseline to compare against.

- Keep results in a workspace directory next to the skill: `iteration-N/` per pass, `with_skill/` and `without_skill/` per case.
- You write `evals/evals.json` by hand. `grading.json`, `timing.json`, and `benchmark.json` come out of the process.
- Start each run with a clean context so the agent follows only `SKILL.md`. Subagents give that isolation, otherwise use a separate session per run.
- Give each run the skill path (or none for the baseline), the prompt, any input files, and the output directory.
- Record timing and token counts so you can weigh quality against cost.

## Assertions

Write them after you see the first outputs, since you often don't know what good looks like until the skill has run.

- Good: "The output file is valid JSON", "The bar chart has labeled axes", "The report includes at least 3 recommendations".
- Weak: "The output is good" (vague), "uses exactly the phrase 'Total Revenue: $X'" (brittle).
- Reserve assertions for objective checks. Style, visual design, and whether output feels right belong in human review.

## Grading

Grade each assertion PASS or FAIL with concrete evidence quoting the output. An LLM judges the subjective ones, a verification script handles mechanical checks like valid JSON or row counts.

- A PASS needs evidence. A section titled "Summary" with one vague sentence is a FAIL.
- Fix assertions that always pass, always fail, or can't be verified.

## Aggregating

Save summary statistics per configuration to `benchmark.json`. The delta shows what the skill costs in time and tokens and what it buys in pass rate.

- Remove assertions that always pass in both configurations, since they inflate the score.
- Investigate assertions that always fail in both: the assertion may be broken or the test too hard.
- Study assertions that pass with the skill and fail without, since that's where the value is.
- Tighten instructions when results are inconsistent across runs, shown by high stddev.
- Read the transcript behind time and token outliers.

## Human review

Assertion grading only checks what you thought to write. Review the actual outputs against the grades and record specific feedback per case. "The chart is missing axis labels" helps, "looks bad" doesn't. Empty feedback means the output looked fine.

## Iterating

Give an LLM the failed assertions, the human feedback, the execution transcripts, and the current `SKILL.md`, and ask for changes.

- Generalize from feedback rather than patching for specific examples.
- Keep the skill lean. When pass rates plateau while you add rules, try removing some.
- Explain the why: "Do X because Y tends to cause Z" beats "ALWAYS do X".
- Bundle repeated work into a tested script in `scripts/`.

Then rerun every case in a new `iteration-<N+1>/`, grade, aggregate, and review. Stop when you're satisfied, feedback is consistently empty, or improvement stalls.
