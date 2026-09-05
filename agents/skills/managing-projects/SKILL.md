---
name: managing-projects
description: Manage project work with beads (the bd CLI) in a git-tracked setup where .beads/issues.jsonl is committed to git. Use when tracking tasks, finding ready work, creating issues with dependencies, claiming and closing work, or syncing issues through git commits and pulls, even when the user doesn't say "beads" or "bd" directly.
---

# Managing projects with beads

Beads (`bd`) is a dependency-aware issue tracker for coding work. Issues form a graph, and `bd ready` shows only the work with no open blockers, so you always know what is actually startable. Use it instead of markdown TODO lists.

This skill covers the git-tracked setup: `.beads/issues.jsonl` is committed to git and travels with your code. The local database is a cache that git hooks rebuild from the JSONL. Git is the sync channel, so pulling code pulls issues and pushing code pushes them.

## When to use bd

Use bd for all task tracking in a project that has a `.beads/` directory. Do not keep a parallel list of tasks in markdown, comments, or a chat message. If a project has no `.beads/` yet, set it up with the steps below before tracking work.

## The git-tracked model

- `.beads/issues.jsonl` is the source of truth that git tracks. Commit it with your code.
- The local database (a `.beads/*.db` SQLite file) is a rebuildable cache. It stays gitignored.
- `bd init` writes `.beads/.gitignore` so the database is ignored and the JSONL and config are kept.
- Git hooks keep the two in step: `pre-commit` exports the database to `.beads/issues.jsonl` and stages it, and `post-merge` plus `post-checkout` import the JSONL back into the local database after a pull or branch switch.

Commit these files: `.beads/issues.jsonl`, `.beads/config.yaml`, `.beads/.gitignore`. Everything else under `.beads/` stays local.

## First-time setup

```sh
bd init            # creates .beads/, config, and .gitignore, installs hooks
bd hooks install   # run again if hooks are missing after a clone or upgrade
git add .beads/issues.jsonl .beads/config.yaml .beads/.gitignore AGENTS.md
git commit -m "Add beads issue tracking"
```

After cloning a repo that already uses bd, run `bd hooks install` once so your checkout gets the export and import hooks. Then `bd import .beads/issues.jsonl` to build the local database from what git already has.

## Daily work loop

1. Pull first so your database matches the team. `git pull` fires the import hook. If hooks are not installed, run `bd import .beads/issues.jsonl` after pulling.
2. Find work: `bd ready --json`.
3. Claim it atomically: `bd update <id> --claim` (sets you as assignee and moves it to in_progress).
4. Do the work. Inspect details any time with `bd show <id>`.
5. Close it: `bd close <id> --reason "What changed"`.
6. Commit and push. The pre-commit hook exports and stages the JSONL, so the issue changes ride along in your commit.

```sh
bd ready --json
bd update bd-a1b2 --claim
bd close bd-a1b2 --reason "Fixed the token refresh race"
git add -A && git commit -m "Fix token refresh race" && git push
```

## Creating issues

Always pass `--json` when you need to read the result programmatically.

```sh
bd create "Title" --description "Full context" -t bug -p 1 --json
```

- Types (`-t`): `bug`, `feature`, `task`, `epic`, `chore`.
- Priorities (`-p`): `0` critical, `1` high, `2` medium (default), `3` low, `4` backlog.
- For descriptions with backticks, quotes, or `!`, pipe stdin: `echo 'text with `backticks`' | bd create "Title" --description=-`.

When you discover new work mid-task, file it and link it to its origin so the trail is clear:

```sh
bd create "Found a related leak" -p 1 --deps discovered-from:<parent-id> --json
```

## Dependencies and ready work

A `blocks` dependency keeps the blocked issue out of `bd ready` until the blocker closes.

```sh
bd dep add <blocked-id> <blocker-id>   # blocked-id depends on blocker-id
bd dep <blocker-id> --blocks <blocked-id>   # same thing, read the other way
bd show <id>          # details and audit trail
bd blocked            # work waiting on open blockers
bd dep tree <id>      # the dependency graph for an issue
```

When a blocker closes, everything it gated shows up in `bd ready` automatically.

## Syncing through git

There is no separate sync command to run. Issues move with normal git:

- `git pull` imports issues created elsewhere (via the post-merge hook).
- `git commit` exports and stages your issue changes (via the pre-commit hook).
- `git push` shares them.

If hooks are not active, do it by hand: `bd export -o .beads/issues.jsonl` before committing, and `bd import .beads/issues.jsonl` after pulling. `bd sync` does both in one step (`--flush-only` exports, `--import-only` imports).

## Resolving JSONL merge conflicts

If a pull leaves a conflict in `.beads/issues.jsonl`, resolve the text conflict first, then import the resolved file. Never export a stale local database over a freshly pulled JSONL, or you will drop the issues other people added.

```sh
# after resolving conflict markers in .beads/issues.jsonl
git add .beads/issues.jsonl
bd import .beads/issues.jsonl
```

Hash-based IDs like `bd-a1b2` mean two people rarely create the same ID, so most concurrent work merges cleanly.

## Gotchas

- Do not use `bd edit`. It opens an interactive editor that agents cannot drive. Use `bd update <id> --description "..."`, `--title`, `--notes`, `--acceptance`, or `--design`.
- Do not commit the local database. Only the JSONL, config, and `.beads/.gitignore` belong in git.
- Pull before you start so your ready queue is current, and confirm `git push` actually succeeds before you consider the session done. Work left unpushed is invisible to everyone else.
- After upgrading bd, run `bd hooks install` again so the hooks match the new binary.
- Run `bd prime` at the start of a session for the project's workflow context and stored memories.
