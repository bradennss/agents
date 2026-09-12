# Evaluating output quality

Structured evals answer whether a skill works reliably across varied prompts, in edge cases, and against a run with no skill at all.

## Designing test cases

A test case has three parts:

- Prompt: a realistic user message, the kind of thing someone would actually type.
- Expected output: a human-readable description of what success looks like.
- Input files (optional): files the skill works with.

Store them in `evals/evals.json` inside the skill directory.

Tips:

- Start with 2 to 3 test cases. Don't over-invest before you see the first results.
- Vary the prompts across phrasing, detail, and formality.
- Cover edge cases, at least one boundary condition like malformed input or an ambiguous request.
- Use realistic context: file paths, column names, personal context. "Process this data" is too vague to test anything.

Don't define pass/fail checks yet. Add those after you see what the first run produces.

## Running evals

Run each test case twice: once with the skill and once without it (or with a previous version). That gives you a baseline to compare against.

Organize results in a workspace directory next to the skill. Each pass gets its own `iteration-N/` directory, and each test case gets `with_skill/` and `without_skill/` subdirectories. You author `evals/evals.json` by hand; other files like `grading.json`, `timing.json`, and `benchmark.json` are produced during the process.

Each run should start with a clean context so the agent follows only what `SKILL.md` says. Subagents give this isolation naturally; without them, use a separate session per run. Provide the skill path (or none for the baseline), the prompt, any input files, and the output directory. When improving an existing skill, snapshot the previous version and point the baseline at it.

Record timing and token counts per run so you can weigh quality against cost.

## Writing assertions

Assertions are verifiable statements about the output. Add them after you see the first outputs, since you often don't know what good looks like until the skill has run.

Good assertions: "The output file is valid JSON" (programmatically verifiable), "The bar chart has labeled axes" (specific and observable), "The report includes at least 3 recommendations" (countable).

Weak assertions: "The output is good" (too vague), "uses exactly the phrase 'Total Revenue: $X'" (too brittle).

Not everything needs an assertion. Style, visual design, and whether output "feels right" are easier to catch in human review. Reserve assertions for objective checks.

## Grading

Grade each assertion against the actual output as PASS or FAIL with concrete evidence that quotes or references the output. Give assertions and outputs to an LLM to judge, and use a verification script for mechanical checks like valid JSON or row counts, since scripts are more reliable and reusable.

Principles:

- Require concrete evidence for a PASS. A section titled "Summary" with one vague sentence is a FAIL.
- Review the assertions themselves while grading. Fix ones that always pass, always fail, or can't be verified.

## Aggregating and analyzing

Compute summary statistics per configuration and save them to `benchmark.json`. The delta tells you what the skill costs (time, tokens) and what it buys (higher pass rate).

Patterns to watch:

- Remove assertions that always pass in both configurations, since they inflate the score without showing skill value.
- Investigate assertions that always fail in both. The assertion may be broken or the test too hard.
- Study assertions that pass with the skill but fail without. That's where the skill adds value; understand why.
- Tighten instructions when results are inconsistent across runs, shown by high stddev. The eval may be flaky or the instructions ambiguous.
- Check time and token outliers by reading the execution transcript.

## Human review

Assertion grading only checks what you thought to write. A human reviewer catches issues you didn't anticipate, like output that's technically correct but misses the point. Review the actual outputs against the grades and record specific, actionable feedback per test case. "The chart is missing axis labels" helps; "looks bad" doesn't. Empty feedback means the output looked fine.

## Iterating

You have three signals: failed assertions (specific gaps), human feedback (broader quality issues), and execution transcripts (why things went wrong). Give all three plus the current `SKILL.md` to an LLM and ask it to propose changes. When you prompt it:

- Generalize from feedback. Fix underlying issues broadly instead of patching narrowly for specific examples.
- Keep the skill lean. A few good instructions often work better than exhaustive rules. If pass rates plateau while you add rules, try removing some.
- Explain the why. "Do X because Y tends to cause Z" works better than "ALWAYS do X."
- Bundle repeated work. If every run rewrites a similar helper, put a tested script in `scripts/`.

The loop: propose improvements, review and apply, rerun all cases in a new `iteration-<N+1>/`, grade and aggregate, review with a human, repeat. Stop when you're satisfied, feedback is consistently empty, or improvement stalls.
