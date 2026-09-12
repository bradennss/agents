# Optimizing descriptions

A skill only helps if it activates. The `description` field is the main signal an agent uses when deciding whether to load a skill. Too narrow and it won't trigger when it should. Too broad and it triggers when it shouldn't.

## How triggering works

At startup the agent loads only each skill's `name` and `description`. When a task matches a description, it reads the full `SKILL.md` and follows it. So the description carries the whole burden of triggering. One nuance: agents usually reach for skills only when a task needs knowledge beyond what they can handle alone. A simple "read this PDF" may not trigger a PDF skill even with a perfect match, because basic tools already cover it. A good description matters for specialized tasks, an unfamiliar API, a domain workflow, or an uncommon format.

## Writing effective descriptions

- Use imperative phrasing. "Use this skill when..." rather than "This skill does...". The agent is deciding whether to act.
- Focus on user intent rather than implementation. Describe what the user wants to achieve, since the agent matches against what the user asked for.
- Err on the side of pushy. List contexts where the skill applies, including ones where the user doesn't name the domain, like "even if they don't explicitly mention 'CSV' or 'analysis.'"
- Keep it concise. A few sentences to a short paragraph. The hard limit is 1024 characters.

## Designing trigger eval queries

Build a set of realistic prompts labeled with whether they should trigger the skill. Aim for about 20: 8 to 10 that should trigger and 8 to 10 that shouldn't.

Should-trigger queries test whether the description captures the skill's scope. Vary them by phrasing (formal, casual, typos), explicitness (some name the domain, some just describe the need), detail (terse and context-heavy), and complexity (single-step and multi-step). The most useful ones are where the skill helps but the connection isn't obvious from the query.

Should-not-trigger queries pay off most as near-misses: prompts that share keywords but need something different. For a CSV analysis skill, "update the formulas in my Excel budget spreadsheet" needs Excel editing, and "write a python script that reads a csv and uploads each row to postgres" is database ETL rather than analysis. Obvious misses like "what's the weather" test nothing.

Make prompts realistic with file paths, personal context, specific details, and casual language.

## Testing whether it triggers

Run each query through the agent with the skill installed and check whether it loaded the `SKILL.md`. Most clients expose logs or tool-call histories that show which skills were consulted.

A query passes if `should_trigger` is true and the skill was invoked, or `should_trigger` is false and it wasn't.

Model behavior is nondeterministic, so run each query about 3 times and compute a trigger rate. A should-trigger query passes if the rate is above a threshold like 0.5; a should-not-trigger query passes if it's below.

## Avoiding overfitting

Split the query set so you don't tune to specific phrasings:

- Train set (about 60%): used to find failures and guide changes.
- Validation set (about 40%): set aside to check whether changes generalize.

Keep a proportional mix of positives and negatives in each, shuffle once, and keep the split fixed.

## The optimization loop

1. Evaluate the current description on both sets. Train guides changes; validation shows whether they generalize.
2. Identify failures in the train set only.
3. Revise the description. If should-trigger queries fail, broaden the scope or add context about when the skill is useful. If should-not-trigger queries fire, add specificity about what the skill does not do. Don't paste in keywords from failed queries, since that overfits; address the general category instead. If stuck, try a structurally different framing. Stay under 1024 characters.
4. Repeat until train queries pass or improvement stalls.
5. Pick the iteration with the best validation pass rate, which may not be the last one.

Five iterations is usually enough. If it isn't improving, the queries may be the problem, too easy, too hard, or mislabeled.

## Applying the result

Update the `description` field, confirm it's under 1024 characters, and sanity-check with a few manual prompts. For a rigorous check, write 5 to 10 fresh queries that were never part of optimization and run them.
