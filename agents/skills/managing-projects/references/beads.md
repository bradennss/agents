# Beads reference

`bd` is a git-backed, dependency-aware issue tracker built for agents. It stores the project as a typed graph in a Dolt database and computes ready work from the dependency edges. This file covers the commands and the semantics the skill relies on. For anything deeper, run `bd <command> --help` or see https://beads.gascity.com/.

## Setup and session start

```bash
bd init                 # once per project: creates .beads/, updates AGENTS.md
bd prime                # print workflow context and stored memory (run each session)
bd ready                # actionable work, no open blockers
```

`bd init` writes the beads workflow into the project's AGENTS.md so any agent discovers it. It uses embedded Dolt by default, single writer, data in `.beads/embeddeddolt/`.

## Creating issues

```bash
bd create "Title"                          # a task by default
bd create "Title" -t epic -p 1             # epic, priority 1
bd create "Title" -t bug -p 0              # P0 bug
bd create "Child" -t task --parent bd-a3f8 # hierarchical child -> bd-a3f8.1
```

Useful `create` flags:

- `-t, --type`: `task`, `bug`, `feature`, `epic`, `chore`, `decision`. Aliases: `feat`, `dec`, `adr`.
- `-p, --priority`: `0` highest through `3` lowest.
- `--parent <id>`: create a hierarchical child and the parent-child link in one step. Preferred over wiring parent-child by hand.
- `--acceptance "<condition>"`: the completion criteria, stored on the bead.
- `--deps <type:id,...>`: wire dependencies at create time, for example `depends-on:bd-20,discovered-from:bd-15`. Watch the direction: `depends-on:X` makes the new issue wait on X, while `blocks:X` makes the new issue block X. A bare `id` means `blocks`, so for "blocked by X" always write `depends-on:X`.
- `--context "<text>"`: extra context, a good place for a spec doc link.
- `-a, --assignee`, `--defer <date>`, `--body-file <path>`.

## Dependencies

Four types matter for this skill:

| Type | Meaning | Use for |
| --- | --- | --- |
| `blocks` (default) | B cannot start until A closes | ordering slices and tasks |
| `parent-child` | children roll up under a parent | epic to task hierarchy |
| `related` | soft link, no blocking | connecting a fix to what it touches |
| `discovered-from` | this was found while doing that | capturing new work without losing its origin |

Adding a dependency:

```bash
bd dep add <issue-id> <depends-on-id>          # issue-id depends on depends-on-id
bd dep add bd-2 bd-1                            # bd-2 depends on bd-1, so bd-1 blocks bd-2
bd dep add bd-2 --depends-on bd-1              # same, explicit flag
bd dep add bd-child bd-epic --type parent-child # child under epic
```

Inspecting:

```bash
bd show <id>            # details, dependencies, audit trail
bd blocked              # everything waiting, and on what
bd dep tree <id>        # dependency tree; use --direction=up to see an epic's children
bd dep cycles           # authoritative cycle check
```

### Direction gotchas

The direction of dependencies is a known source of mistakes. Keep these straight:

- `bd dep add A B` always means A depends on B. The first argument is the blocked or child issue, the second is the blocker or parent.
- For epics, prefer `--parent` at create time instead of wiring parent-child afterward. The manual argument order is easy to reverse and beads will reject the wrong order with an error.
- `bd show <epic>` lists children under a "Blocks" heading, and `bd dep tree <epic>` shows nothing unless you pass `--direction=up`. This is a display quirk, not a broken graph.
- `bd dep list` output does not reveal edge direction on its own. Use `bd show` or `bd dep tree` when direction matters.

## Working a task

```bash
bd ready                       # find unblocked work
bd update <id> --claim         # take it: sets assignee and in_progress
bd update <id> -p 0            # change priority
bd close <id> -r "verified how" # finish; releases anything it blocked
```

Always claim before starting so the graph reflects who is doing what. Always close with real verification in the message.

## Memory

```bash
bd remember "durable insight or decision"   # stored project memory
bd prime                                    # injects memories into context
```

Use memory for decisions, conventions, and gotchas a future session needs. Do not create MEMORY.md or scatter notes in the repo.

## Sync and storage

- The Dolt database in `.beads/` is the source of truth. `.beads/issues.jsonl` is an export for viewers and git diffs, not a backup and not editable state.
- Sync across machines with `bd dolt push` and `bd dolt pull`. These move data over `refs/dolt/data` on the git remote.
- Back up with `bd backup` or `bd export --all`. Do this before any destructive command.

## Safety

- Back up before `bd doctor --fix`. Agent-driven repair has deleted entire databases. Run `bd export --all` first.
- On upgrades, if `bd` reports a schema version mismatch, the binary is stale relative to the database. Update the binary rather than forcing past the guard.
- In git worktrees, `bd doctor` can report false git failures. Trust `bd dep cycles` over `bd doctor` for cycle checks.

## Quick command index

```bash
bd init                        bd prime
bd ready                       bd ready --json
bd blocked                     bd show <id>
bd create "Title" -t <type> -p <n>
bd create "Title" --parent <id> --acceptance "..." --deps depends-on:<id>
bd update <id> --claim         bd close <id> -r "verified how"
bd dep add <blocked> <blocker> bd dep tree <id> --direction=up
bd dep cycles                  bd remember "insight"
bd dolt push                   bd dolt pull
bd backup                      bd export --all
```
