# AGENTS.md

Shared config for my coding agents.

## Structure

- `agents/AGENTS.md`: the global rules, shared by every project, routing to the skills.
- `agents/skills/`: one folder per skill, each with a `SKILL.md`. `working-collaboratively` and `working-autonomously` are the two operation modes, the rest are workflows the rules route to.

## Working here

- This file covers only this repo. The global rules live in `agents/AGENTS.md`, so don't repeat them here.
- Edits under `agents/` go live through the symlink. Every agent picks them up on its next start, with no reinstall.

## Verifying changes

```sh
pnpm run format:check
pnpm run validate
```

`validate` checks each skill's frontmatter and naming.
