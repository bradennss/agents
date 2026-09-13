# Optimizing descriptions

A skill only helps if it activates, and `description` is the main signal. Too narrow and it won't trigger when it should, too broad and it triggers when it shouldn't.

At startup the agent loads only each skill's `name` and `description`, then reads the full `SKILL.md` when a task matches. Agents reach for skills only when a task needs more than they can handle alone, so "read this PDF" may not trigger a PDF skill however well it matches. Descriptions matter most for specialized tasks, unfamiliar APIs, domain workflows, and uncommon formats.

## Writing one

- Imperative phrasing: "Use this skill when...", not "This skill does...".
- Focus on user intent rather than implementation, since the agent matches against what the user asked for.
- Err on the side of pushy. List the contexts where it applies, including ones where the user doesn't name the domain, like "even if they don't explicitly mention 'CSV' or 'analysis'".
- A few sentences to a short paragraph. Hard limit 1024 characters.

## Trigger eval queries

Build about 20 realistic prompts labeled with whether they should trigger the skill, 8 to 10 each way.

- Should-trigger: vary phrasing (formal, casual, typos), explicitness, detail, and complexity. The useful ones are where the skill helps but the connection isn't obvious.
- Should-not-trigger: near-misses pay off most. For a CSV analysis skill, "update the formulas in my Excel budget spreadsheet" needs Excel editing, and "write a python script that reads a csv and uploads each row to postgres" is ETL. "What's the weather" tests nothing.
- Make prompts realistic: file paths, personal context, specific details, casual language.

## Testing

Run each query with the skill installed and check whether the `SKILL.md` loaded, which most clients show in logs or tool-call histories. A pass is a should-trigger query that loaded it, or a should-not-trigger query that didn't. Behavior is nondeterministic, so run each query about 3 times and compare the trigger rate against a threshold like 0.5.

Split the queries so you don't tune to phrasings: about 60% train, 40% validation, a proportional mix of positives and negatives in each, shuffled once and then fixed.

## The optimization loop

1. Evaluate on both sets.
2. Find failures in the train set only.
3. Revise. Should-trigger failures need broader scope or more context about when the skill is useful. Should-not-trigger firings need specificity about what the skill doesn't do. Address the general category rather than pasting in keywords, which overfits. Try a structurally different framing when stuck. Stay under 1024 characters.
4. Repeat until train queries pass or improvement stalls, usually within five iterations. No improvement means the queries may be too easy, too hard, or mislabeled.
5. Pick the iteration with the best validation pass rate, which may not be the last.

Then update the field, confirm the length, and sanity-check with 5 to 10 fresh queries that were never part of the optimization.
