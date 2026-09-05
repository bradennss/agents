# AGENTS.md

This repo holds shared config for my coding agents. `INSTALL.md` symlinks everything under `agents/` into each agent's own global config.

## Structure

- `agents/AGENTS.md` — the global rules, shared by every project.
- `agents/skills/` — the skills, one folder each with a `SKILL.md`.

## Working here

- The root `AGENTS.md` covers only this repo. The global rules live in `agents/AGENTS.md`, so don't repeat them here.
- Edits under `agents/` go live through the symlink. Change `agents/AGENTS.md` or add a folder under `agents/skills/`, and every agent picks it up on its next start with no reinstall.
