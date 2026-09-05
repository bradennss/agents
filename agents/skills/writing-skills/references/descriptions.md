# Tuning descriptions so the skill triggers

The `description` field decides whether a skill loads. Too narrow and it won't trigger when it should; too broad and it triggers when it shouldn't. This is the test-and-tune loop from https://agentskills.io/skill-creation/optimizing-descriptions.

## How triggering works

At startup the agent loads only each skill's `name` and `description`. When a task matches a description, it loads the full `SKILL.md` and follows it. So the description carries the whole triggering burden.

One nuance: agents usually reach for a skill only when a task needs knowledge or steps beyond what they can do alone. A one-step "read this PDF" may not trigger a PDF skill even with a perfect description, because basic tools cover it. Skills help most on tasks with an unfamiliar API, a domain workflow, or an uncommon format.

## What a good description looks like

- Imperative phrasing aimed at the agent: "Use this skill when..." not "This skill does...". The agent is deciding whether to act.
- User intent, not internal mechanics. The agent matches what the user asked for.
- A bit pushy. List the contexts where it applies, including ones where the user won't name the domain: "even if they don't explicitly mention 'CSV' or 'analysis.'"
- Concise. A few sentences to a short paragraph. Hard limit 1024 characters.

Before and after:

```yaml
# Before
description: Process CSV files.

# After
description: >
  Analyze CSV and tabular data files: compute summary statistics, add derived columns, generate charts, and clean messy data. Use this skill when the user has a CSV, TSV, or Excel file and wants to explore, transform, or visualize the data, even if they don't explicitly mention "CSV" or "analysis."
```

The improved one is more specific about what it does and broader about when it applies.

## Build a set of eval queries

Write realistic user prompts labeled with whether they should trigger the skill. Aim for about 20: 8-10 that should trigger and 8-10 that shouldn't.

```json
[
  {
    "query": "I've got a spreadsheet in ~/data/q4.xlsx with revenue in col C and expenses in col D, can you add a profit margin column and highlight anything under 10%?",
    "should_trigger": true
  },
  {
    "query": "whats the quickest way to convert this json file to yaml",
    "should_trigger": false
  }
]
```

Should-trigger queries test whether the description covers the scope. Vary them:

- Phrasing: some formal, some casual, some with typos or abbreviations.
- Explicitness: some name the domain ("analyze this CSV"), some describe the need without naming it ("my boss wants a chart from this data file").
- Detail: mix terse prompts with context-heavy ones (file paths, column names, backstory).
- Complexity: single-step tasks alongside multi-step workflows, so you test whether the agent spots the skill when the relevant part is buried.

The most useful positive queries are ones where the skill helps but the link isn't obvious. If the query already asks for exactly what the skill does, any description triggers.

Should-not-trigger queries test precision. The valuable ones are near-misses: queries that share keywords but need something else.

- Weak (tests nothing): "Write a fibonacci function", "What's the weather?"
- Strong: "I need to update the formulas in my Excel budget spreadsheet" (shares "spreadsheet" but needs Excel editing, not CSV analysis); "write a python script that reads a csv and uploads each row to postgres" (involves CSV, but it's database ETL, not analysis).

Make queries realistic: file paths, personal context ("my manager asked me to..."), specific column and company names, casual language, occasional typos.

## Test whether it triggers

Run each query through the agent with the skill installed and see whether it loads the skill. Most clients expose logs or tool-call history that show which skills were consulted. A query passes when should_trigger is true and the skill loaded, or should_trigger is false and it didn't.

Model behavior is nondeterministic, so run each query a few times (3 is a fine start) and compute a trigger rate, the fraction of runs that loaded the skill. A should-trigger query passes if its rate is above a threshold (0.5 is a fine default); a should-not-trigger query passes if it's below.

Script it. Fill in `check_triggered` with your agent's detection logic: run the agent on the query and return 0 if it loaded the skill, 1 if it didn't. Most clients expose logs, a tool-call history, or a machine-readable output mode you can grep for the skill load.

```bash
#!/bin/bash
QUERIES_FILE="${1:?Usage: $0 <queries.json>}"
SKILL_NAME="my-skill"
RUNS=3

# Replace the body with a call to your agent and a check for the skill load.
# Example shape: run the agent, capture its structured output, and look for a
# tool call that loaded SKILL_NAME.
check_triggered() {
  local query="$1"
  run_agent --prompt "$query" --output json 2>/dev/null \
    | grep -q "\"skill\":[[:space:]]*\"$SKILL_NAME\""
}

count=$(jq length "$QUERIES_FILE")
for i in $(seq 0 $((count - 1))); do
  query=$(jq -r ".[$i].query" "$QUERIES_FILE")
  should_trigger=$(jq -r ".[$i].should_trigger" "$QUERIES_FILE")
  triggers=0
  for run in $(seq 1 $RUNS); do
    check_triggered "$query" && triggers=$((triggers + 1))
  done
  jq -n --arg query "$query" --argjson should_trigger "$should_trigger" \
    --argjson triggers "$triggers" --argjson runs "$RUNS" \
    '{query: $query, should_trigger: $should_trigger, triggers: $triggers, runs: $runs, trigger_rate: ($triggers / $runs)}'
done | jq -s '.'
```

## Avoid overfitting

If you tune against every query, you risk a description that fits these exact phrasings but fails on new ones. Split the set:

- Train (~60%): the queries you use to find failures and guide changes.
- Validation (~40%): held back, used only to check whether changes generalize.

Keep a proportional mix of positives and negatives in both, shuffle once, and keep the split fixed across iterations.

## The loop

1. Evaluate the current description on both sets. Train guides changes; validation tells you if they generalize.
2. Find train-set failures: which should-trigger queries missed, which should-not-trigger queries fired. Use only train failures to guide changes.
3. Revise the description:
   - Missed should-trigger queries mean it's too narrow. Broaden the scope or add context about when the skill helps.
   - False-firing should-not-trigger queries mean it's too broad. Add specificity about what the skill does not do, or clarify the boundary with adjacent skills.
   - Don't paste in keywords from failed queries; that's overfitting. Find the general category they represent and address that.
   - Stuck after a few tries? Try a structurally different description instead of small tweaks.
   - Keep it under 1024 characters. Descriptions grow during tuning.
4. Repeat until train queries pass or improvement stalls.
5. Pick the iteration with the best validation pass rate. The best one may not be the last; a later version can overfit.

Five iterations is usually enough. If it's not improving, the queries may be the problem (too easy, too hard, or mislabeled), not the description.

## Apply and sanity-check

1. Update the `description` in `SKILL.md`.
2. Confirm it's under 1024 characters.
3. Write 5-10 fresh queries the tuning never saw and run them for an honest check that the description generalizes.
