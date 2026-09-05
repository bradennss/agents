---
name: managing-projects
description: >
  Track project work with beads (bd), a git-backed issue tracker built for coding agents, with dependency graphs and multi-agent coordination. Use when the user wants to plan a project, break work into tasks, track progress, manage dependencies, or find the next thing to work on, even if they don't say "beads" or "bd" by name. Also use before starting any multi-step build so the plan lives in bd instead of a throwaway checklist.
---

# Managing projects with beads

Use [beads](https://github.com/steveyegge/beads) (the `bd` command) to track tasks
instead of a markdown checklist. It keeps a dependency-aware issue graph in git, so
plans survive across sessions, compaction, and crashes, and several agents can work
without stepping on each other.

Beads ships its own workflow instructions. Don't duplicate them here. Load them from `bd`.

## First thing: load bd's own guidance

Run this at the start of any project work:

```bash
bd prime
```

That prints bd's up-to-date workflow context (roughly 80 lines): the create, claim,
and close loop, dependency commands, and current conventions. Follow what it says. If
`bd prime` prints nothing or errors, bd isn't set up in this repo yet, so see below.

## If the repo isn't using bd yet

Check with `bd ready`. If it fails, set bd up:

```bash
bd init          # create the beads database in this repo
bd setup codex   # write the AGENTS.md pointer and hooks (swap in your agent: claude, cursor, copilot, gemini, aider, factory)
```

Run `bd setup --list` to see every supported agent. After setup, run `bd prime` and
carry on.

## The short version

If you only remember a few commands, `bd prime` covers the rest:

- `bd ready` — find unblocked work
- `bd create "Title" --type task --priority 2` — add an issue
- `bd show <id>` — read one issue
- `bd update <id> --claim` — take an issue before you start it
- `bd close <id>` — finish it
- `bd dolt push` — push the beads database to the remote

Always run `bd prime` first, since these commands and their flags can change with the
installed version.
