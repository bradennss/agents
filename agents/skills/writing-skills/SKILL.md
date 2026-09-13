---
name: writing-skills
description: Write and refine Agent Skills in the agentskills.io open format. Use when writing a new SKILL.md, packaging domain knowledge into a reusable skill, splitting a skill into reference and script files, tuning a description so it triggers reliably, or judging the quality of a skill's output, even when the user never says the word "skill".
---

# Writing skills

A skill is a folder with a `SKILL.md`: YAML frontmatter, then Markdown instructions. Agents load skills in stages, so keep the always-loaded part small and push the bulk into reference files.

Build a skill when a task needs knowledge the agent lacks: a specific API, a project convention, a domain workflow, an uncommon format. Skip it when the agent already handles the task.

## Layout

```
skill-name/
├── SKILL.md          # required: frontmatter + core instructions
├── references/       # optional: docs loaded on demand
├── scripts/          # optional: runnable code
└── assets/           # optional: templates, data, images
```

```yaml
---
name: my-skill # required, 1-64 chars, lowercase a-z 0-9 and hyphens, matches the folder
description: ... # required, 1-1024 chars, what it does and when to use it
---
```

Optional fields: `license`, `compatibility`, `metadata`, `allowed-tools`. Full rules in `references/specification.md`.

## Workflow

1. Ground it in real expertise: do the task once with an agent, or build it from real project artifacts. Don't ask an LLM to invent one from generic knowledge. See `references/best-practices.md`.
2. Write the body, including only what the agent would get wrong without it.
3. Keep `SKILL.md` under 500 lines and about 5,000 tokens. Move deep or conditional material into `references/`, and say when to read each file.
4. Write a triggering description: imperative, about user intent, listing the contexts where it applies. See `references/descriptions.md`.
5. Validate with `skills-ref validate ./skill-name`.
6. Refine with real runs and cut what doesn't earn its place. See `references/evaluating.md`.

## Writing the body

- Add what the agent lacks, cut what it already knows.
- One coherent responsibility per skill, scoped the way you'd scope a function.
- Match specificity to fragility: leave room where many approaches work and explain why, be prescriptive where a sequence is fragile or consistency matters.
- Give a default rather than a menu of equal options.
- Teach how to approach a class of problems rather than what to produce once.

Patterns that help: gotchas lists, output templates, checklists for multi-step work, and validation loops where the agent checks its own output. Details in `references/best-practices.md`.

## Reference files

- `references/specification.md`: frontmatter fields, directory rules, progressive disclosure, file references, validation.
- `references/best-practices.md`: sourcing expertise, scoping, calibrating control, instruction patterns.
- `references/descriptions.md`: testing and tuning the `description` so it triggers.
- `references/scripts.md`: one-off commands, self-contained scripts, script interfaces for agents.
- `references/evaluating.md`: test cases, assertions, grading, iterating on output quality.
