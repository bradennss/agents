# AGENTS.md

Shared config for my coding agents.

## Structure

- `agents/AGENTS.md` holds the global rules, shared by every project, and routes to the skills.
- `agents/skills/` holds the skills, one folder each with a `SKILL.md`. `working-collaboratively` and `working-autonomously` are the two operation modes, and the rest are workflows the rules route to.

## Working here

- This root `AGENTS.md` covers only this repo. The global rules live in `agents/AGENTS.md`, so don't repeat them here.
- Edits under `agents/` go live through the symlink. Change `agents/AGENTS.md` or add a folder under `agents/skills/`, and every agent picks it up on its next start with no reinstall.

## Verifying changes

```sh
pnpm run format:check
npx skills-ref validate agents/skills/*
```
