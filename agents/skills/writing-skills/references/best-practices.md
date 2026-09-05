# Best practices

How to write skills that are well scoped and calibrated to the task.

## Start from real expertise

The common pitfall is asking an LLM to generate a skill from its general training knowledge. The result is vague, generic procedures like "handle errors appropriately" instead of the specific patterns, edge cases, and conventions that make a skill valuable. Feed real domain context into the process.

### Extract from a hands-on task

Do a real task with an agent, giving context, corrections, and preferences along the way. Then pull the reusable pattern into a skill. Pay attention to:

- Steps that worked, the sequence that led to success.
- Corrections you made, places where you steered the agent, like "use library X instead of Y."
- Input and output formats, what the data looked like going in and coming out.
- Context you provided, project facts and constraints the agent didn't already know.

### Synthesize from existing artifacts

When you have a body of knowledge, feed it to an LLM and ask it to synthesize a skill. Project-specific material beats generic references. Good sources: internal docs, runbooks, and style guides; API specs, schemas, and config files; code review comments and issue trackers; version control history, especially patches and fixes; and real failure cases with their resolutions.

## Refine with real execution

The first draft usually needs work. Run the skill against real tasks, then feed all the results back, not just the failures. Ask what triggered false positives, what was missed, and what could be cut. Even one round of execute-then-revise helps. See `evaluating.md` for a structured approach.

## Spend context wisely

Once a skill activates, its whole body competes for the agent's attention with everything else in the window.

- Add what the agent lacks, omit what it knows. For each piece, ask "would the agent get this wrong without this instruction?" If no, cut it. If the agent already handles the whole task without the skill, the skill may not add value.
- Design coherent units. Scope a skill like a function: one coherent unit of work that composes with others. Too narrow forces many skills to load at once. Too broad is hard to trigger precisely. Querying a database and formatting results may be one unit; adding database administration is too much.
- Aim for moderate detail. Concise stepwise guidance with a working example beats exhaustive docs. Covering every edge case can send the agent down unproductive paths.
- Structure large skills with progressive disclosure. Keep the core in `SKILL.md`, move detail into `references/`, and say when to load each file. "Read `references/api-errors.md` if the API returns a non-200 status" beats "see references/ for details."

## Calibrate control

Match the specificity of instructions to how fragile the task is.

- Give the agent freedom when many approaches are valid. Explaining why works better than rigid rules, since an agent that understands the purpose makes better context-dependent choices.
- Be prescriptive when operations are fragile, consistency matters, or a specific sequence is required.
- Provide defaults, not menus. Pick a default and mention alternatives briefly rather than listing equal options.
- Favor procedures over declarations. Teach how to approach a class of problems, not what to produce for one instance. Specific details like output templates or "never output PII" still belong; the approach should generalize.

## Patterns for effective instructions

Use the ones that fit your task.

- Gotchas sections. Concrete corrections to mistakes the agent will make without being told, like environment facts that defy reasonable assumptions. Keep them in `SKILL.md` so the agent reads them before hitting the situation.
- Output templates. Give a template when you need a specific format. Agents pattern-match against concrete structures better than prose. Keep short templates inline; store long ones in `assets/`.
- Checklists. Help the agent track progress on multi-step work with dependencies or validation gates.
- Validation loops. Have the agent do the work, run a validator, fix issues, and repeat until it passes. A reference document can act as the validator.
- Plan-validate-execute. For batch or destructive work, have the agent write an intermediate plan in a structured format, validate it against a source of truth, then execute. Clear validation errors let the agent self-correct.
- Bundle reusable scripts. If the agent reinvents the same logic each run, write a tested script once and put it in `scripts/`. See `scripts.md`.
