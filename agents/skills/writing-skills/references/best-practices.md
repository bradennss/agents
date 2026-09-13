# Best practices

## Start from real expertise

An LLM asked to generate a skill from general training knowledge returns vague procedures like "handle errors appropriately" instead of the patterns, edge cases, and conventions worth loading. Feed it real context instead.

From a hands-on task, do the task with an agent and pull the reusable pattern out. Pay attention to:

- The sequence of steps that worked.
- Corrections you made, like "use library X instead of Y".
- Input and output formats.
- Project facts and constraints the agent didn't know.

From existing artifacts, feed a body of knowledge to an LLM and ask it to synthesize a skill. Project-specific material beats generic references: internal docs, runbooks, style guides, API specs, schemas, config files, code review comments, issue trackers, version control history, and real failure cases with their resolutions.

## Refine with real execution

Run the first draft against real tasks, then feed all the results back, successes as well as failures. Ask what triggered false positives, what was missed, and what could be cut. One round of execute-then-revise helps. See `evaluating.md`.

## Spend context wisely

An active skill's whole body competes for the agent's attention.

- For each piece, ask whether the agent would get it wrong without the instruction. If no, cut it. If the agent handles the whole task without the skill, the skill may not be worth having.
- Scope a skill like a function: one coherent unit that composes with others. Too narrow forces many skills to load at once, too broad is hard to trigger precisely. Querying a database and formatting results may be one unit, adding database administration is too much.
- Aim for moderate detail. Short stepwise guidance with a working example beats exhaustive docs, which can send the agent down unproductive paths.
- Use progressive disclosure for large skills: core in `SKILL.md`, detail in `references/`, with a condition for loading each file. "Read `references/api-errors.md` if the API returns a non-200 status" works better than "see references/ for details".

## Calibrate control

- Give freedom where many approaches are valid, and explain why, since an agent that understands the purpose makes better context-dependent choices.
- Be prescriptive where operations are fragile, consistency matters, or a sequence is required.
- Give a default rather than a menu, mentioning alternatives briefly.
- Favor procedures over declarations. Specific details like output templates or "never output PII" still belong.

## Instruction patterns

- Gotchas: concrete corrections to mistakes the agent makes unprompted, like environment facts that defy assumptions. Keep them in `SKILL.md`.
- Output templates: agents pattern-match structures more reliably than prose. Short ones inline, long ones in `assets/`.
- Checklists: for multi-step work with dependencies or validation steps.
- Validation loops: do the work, run a validator, fix, repeat until it passes. A reference document can be the validator.
- Plan-validate-execute: for batch or destructive work, write the plan in a structured format, validate it against a source of truth, then execute. Clear validation errors let the agent self-correct.
- Bundled scripts: when the agent reinvents the same logic each run, write a tested script once in `scripts/`. See `scripts.md`.
