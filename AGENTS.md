# AGENTS.md

Shared config for my coding agents.

## Structure

- `agents/AGENTS.md` holds the global rules, shared by every project.
- `agents/skills/` holds the skills, one folder each with a `SKILL.md`.

## Working here

- This root `AGENTS.md` covers only this repo. The global rules live in `agents/AGENTS.md`, so don't repeat them here.
- Edits under `agents/` go live through the symlink. Change `agents/AGENTS.md` or add a folder under `agents/skills/`, and every agent picks it up on its next start with no reinstall.

## Verifying changes

```sh
pnpm run format:check
```
