# Maintain phase: pull queue

Once the product is live, stop making big plans and run continuous flow. The graph is now a steady queue of bugs, chores, small features, and follow-ups. `bd ready` is the queue.

## The flow

1. `bd prime` at the start of a session, then `bd ready` to see what is actionable.
2. Pull the highest-priority ready item. Claim it, do it, verify it, close it.
3. Finish before you start the next one. Keep work in progress low so nothing rots half-done.
4. File new work the moment it appears, then keep going on your current task unless the new work blocks you.

## Triage new work

Every bug, chore, or idea becomes a bead right away, with a priority and a link to its cause.

```bash
bd create "Login throttling off by one" -t bug -p 1 --deps related:bd-a3f8
bd create "Upgrade to node 24" -t chore -p 3
bd create "Add rate-limit metrics" -t task --deps discovered-from:bd-91 -p 2
```

Priorities, highest first:

- `-p 0`: broken in production, drop everything.
- `-p 1`: important, do soon.
- `-p 2`: normal.
- `-p 3`: nice to have, do when idle.

Use `related` to connect a fix to the thing it touches, and `discovered-from` when the work surfaced while doing something else. That keeps the history queryable later.

## Batch themed work

When maintenance clusters around a theme, like a dependency upgrade or a cleanup pass, group it under an epic so it stays visible and does not get lost between one-off fixes. Do not force a heavy plan for routine flow. The epic is just a folder for related beads.

```bash
bd create "Dependency upgrade pass Q2" -t epic -p 2       # -> bd-up
bd create "Upgrade framework major version" -t task --parent bd-up
bd create "Fix breaking changes in auth" -t task --parent bd-up --deps depends-on:bd-up.1
```

## Keep the graph healthy

- Close beads promptly with real verification in the message. A stale in-progress bead lies about the state of the project.
- Let compaction summarize old closed work so the graph stays cheap on context. Do not delete history you might need.
- Re-run `bd ready` and reprioritize as reality changes. A `-p 3` that starts hurting users becomes a `-p 1`.
- Sync across machines with `bd dolt push` and `bd dolt pull`, not by editing the JSONL export.

## When maintenance turns into a project

If a stream of related beads is really a new capability in disguise, stop treating it as flow and go back to the design phase. Write a spec, decompose it, and build it as slices. The signal is that the beads keep blocking each other and need real up-front sequencing.
