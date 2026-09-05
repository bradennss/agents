---
name: writing-skills
description: Create, structure, and refine Agent Skills that follow the agentskills.io open format. Use when writing a new SKILL.md, packaging domain knowledge into a reusable skill, splitting a skill into reference and script files, tuning a description so it triggers reliably, or evaluating a skill's output, even when the user doesn't say "skill" directly.
---

# Writing skills

A skill is a folder with a `SKILL.md` file that gives an agent a reusable capability. The file has YAML frontmatter plus Markdown instructions. Agents load skills progressively, so keep the always-needed parts small and push the bulk into reference files that load on demand.

## When to build a skill

Build a skill when a task needs knowledge the agent lacks on its own: a specific API, a project convention, a domain workflow, or an uncommon format. If the agent already handles the task well without help, skip the skill.

## Directory layout

```
skill-name/
├── SKILL.md          # required: frontmatter + core instructions
├── references/       # optional: docs loaded on demand
├── scripts/          # optional: runnable code
└── assets/           # optional: templates, data, images
```

The folder name must match the `name` field.

## Frontmatter quick reference

```yaml
---
name: my-skill # required, 1-64 chars, lowercase a-z 0-9 and hyphens, matches folder
description: ... # required, 1-1024 chars, says what it does AND when to use it
---
```

Optional fields: `license`, `compatibility` (env needs), `metadata` (string map), `allowed-tools` (experimental). Full rules in `references/specification.md`.

## Core workflow

1. Ground the skill in real expertise. Do the task once with an agent, or synthesize from real project artifacts. Don't ask an LLM to invent a skill from generic knowledge. See `references/best-practices.md`.
2. Write the body. Include only what the agent would get wrong without it. Prefer concise stepwise guidance with one working example over exhaustive docs.
3. Keep `SKILL.md` under 500 lines and about 5,000 tokens. Move deep or conditional material into `references/`, and tell the agent when to read each file.
4. Write a triggering description. Use imperative phrasing, focus on user intent, and list contexts where it applies. See `references/descriptions.md`.
5. Validate the format with `skills-ref validate ./skill-name`.
6. Refine with real runs. Feed results back and cut what doesn't earn its place. See `references/evaluating.md`.

## Writing the body

- Add what the agent lacks, cut what it already knows.
- Keep one coherent responsibility per skill, like one function.
- Match specificity to fragility. Give freedom where many approaches work and explain why. Be prescriptive where a sequence is fragile or consistency matters.
- Give defaults, not menus of equal options.
- Teach how to approach a class of problems, not what to produce for one instance.

Useful patterns: gotchas lists for non-obvious environment facts, output templates the agent can pattern-match, checklists for multi-step work, and validation loops where the agent checks its own output before finishing. Details in `references/best-practices.md`.

## Reference files

Read these when the task calls for the depth:

- `references/specification.md` for exact frontmatter fields, directory rules, progressive disclosure, file reference rules, and validation.
- `references/best-practices.md` for sourcing expertise, scoping, calibrating control, and instruction patterns.
- `references/descriptions.md` for testing and tuning the `description` so it triggers on the right prompts.
- `references/scripts.md` for one-off commands, self-contained scripts, and designing script interfaces for agents.
- `references/evaluating.md` for test cases, assertions, grading, and iterating on output quality.
