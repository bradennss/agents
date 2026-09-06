---
name: managing-projects
description: Plan, build, and maintain a software project across its full lifecycle using beads (bd) for task tracking. Use when starting a new project, turning an idea or spec into an epic and task graph, sequencing a large build into end-to-end slices, deciding what to work on next, tracking long-horizon work across sessions, or keeping a mature codebase healthy. Applies whenever work spans many tasks or outlives a single session, even when the user doesn't say "beads", "roadmap", or "project management" directly.
---

# Managing projects

Run a project as one dependency-aware task graph in beads (`bd`), and match the process to the project's phase. Design work needs a spec. Build work needs end-to-end slices. Maintenance work needs a steady pull queue. The graph is the same throughout, so the project keeps its memory as it moves between phases and across sessions.

Use this skill for any work that spans many tasks or more than one session. For a single small change, skip it and just do the work.

## The model

One project is one beads graph.

- A **spec** or PRD is the source of truth for what and why. It lives in the repo as a doc.
- An **epic** bead is one outcome, usually one vertical slice of the product.
- A **task** bead is one atomic, verifiable unit of work under an epic.
- **Dependencies** (`blocks`, `parent-child`, `related`, `discovered-from`) encode the order and the structure. `bd ready` reads the graph and tells you what is unblocked right now.

The project moves through three phases. Read the matching reference file when you enter a phase.

| Phase | Question | Method | Reference |
| --- | --- | --- | --- |
| Design | What are we building and why? | Spec-driven | `references/design-phase.md` |
| Build | How do we get it working end to end? | Vertical slices | `references/decomposition.md` |
| Maintain | What is worth doing next? | Pull queue | `references/maintain-phase.md` |

## Setup

Do this once per project. Beads is installed system-wide, not cloned into the repo.

```bash
cd your-project
bd init            # creates .beads/ and updates AGENTS.md with the bd workflow
bd prime           # print workflow context and stored project memory
```

At the start of any session, run `bd prime` to reload context, then `bd ready` to see actionable work. Do not track tasks in markdown TODO lists. The graph is the tracker.

## The loop, every phase

Whatever the phase, work runs the same loop:

1. `bd ready` to find unblocked work, or `bd show <id>` to open a specific task.
2. `bd update <id> --claim` before you start, so the task shows as in progress.
3. Do the work. If you discover new work, file it now with `--deps discovered-from:<id>` so it links back and nothing is lost.
4. Prove it works end to end, per the global rules. Automated checks are not enough on their own.
5. `bd close <id> -r "what changed and how it was verified"`. Closing releases anything it blocked, so new work appears in `bd ready`.

Never mark a task done without evidence it works. Never let one agent both build and sign off its own work without a real check.

## Phase 1: Design

Turn intent into a spec and an epic graph before writing product code. Read `references/design-phase.md` for the spec template and the full flow. In short:

1. Write the spec: goals, non-goals, requirements with acceptance criteria, constraints, risks. Keep technology choices out until the requirements are clear.
2. Clarify. Turn every ambiguity or contradiction into a `decision` bead, not a silent assumption. Record the outcome with `bd remember` so it survives compaction and future sessions.
3. Write the technical plan: architecture, interfaces, data contracts, and how each piece gets verified.
4. Decompose into epics (one per slice) and tasks. Wire dependencies. This produces the graph the build phase runs against.

Store durable decisions as memory, not as loose files:

```bash
bd remember "Auth uses short-lived JWTs, refresh tokens in httpOnly cookies. Decided in bd-a3f8."
```

## Phase 2: Build

Grow the product as thin end-to-end slices, never as separate horizontal layers. Read `references/decomposition.md` for slicing rules and milestone exit criteria. In short:

1. Build a walking skeleton first: one tiny but real flow through every layer the product needs, including CI. This is production code you keep, not a throwaway.
2. Make each later epic a slice that delivers a usable capability across all its layers. Decompose by user outcome, like "user saves and reloads a display name", not by "database" then "API" then "frontend".
3. Wire the graph so a slice that needs another slice's output is blocked by it. Leave independent slices unblocked so they surface together in `bd ready`.
4. Add an explicit integration task after parallel slices. Tasks that look independent often share a hidden interface.
5. A slice is done when it works end to end in a production-like environment with automated verification, not when a component is "90% done".

Create an epic and its children so the IDs stay hierarchical:

```bash
bd create "Display name settings" --type epic          # -> bd-a3f8
bd create "Save name end to end" --type task --parent bd-a3f8   # -> bd-a3f8.1
bd create "Reload name on login" --type task --parent bd-a3f8 --deps depends-on:bd-a3f8.1
```

## Phase 3: Maintain

Once the product is live, run continuous flow instead of big plans. Read `references/maintain-phase.md`. In short:

- `bd ready` is the pull queue. Prioritize with `-p 0` (highest) through `-p 3`, and pull the top item when you have capacity.
- Keep work in progress low. Finish and close before you claim the next thing.
- File every bug, chore, and follow-up as a bead the moment it appears. Link fixes to their cause with `related` or `discovered-from`.
- Group a themed batch of maintenance work under an epic so it stays visible, but do not force a heavy plan for routine flow.

## Task hygiene

These rules keep the graph trustworthy. They apply in every phase.

- **Atomic and verifiable.** One task is one thing an agent can finish and check in a single focused context. If you cannot state its acceptance criteria, split it.
- **Acceptance up front.** Put the completion condition in the bead with `--acceptance` when you create it.
- **Capture discovered work immediately.** Use `--deps discovered-from:<id>`. Do not stop your current task to do the new work unless it blocks you.
- **Persist decisions, not files.** Use `bd remember` for anything a future session needs to know. Do not create MEMORY.md or scatter notes.
- **Replan only the affected subgraph.** When a requirement changes, invalidate just the downstream tasks and re-plan that branch. Do not regenerate the whole graph.

## Appetite, to kill rabbit holes

Before starting an epic, set an appetite: how much time the outcome is worth, not how long it might take. When the work threatens to exceed the appetite, stop and cut scope to fit rather than chasing it. Trim the solution, split a follow-up bead, or drop a non-goal. Fixed appetite with variable scope keeps a long project from stalling in one corner.

## Beads essentials

```bash
bd ready                       # unblocked work, the pull queue
bd ready --json                # same, machine readable
bd blocked                     # what is waiting and on what
bd show <id>                   # details, dependencies, audit trail
bd create "Title" -p 1 -t task # new task, priority 1
bd create "Title" -t epic      # new epic
bd update <id> --claim         # take a task (assignee + in_progress)
bd close <id> -r "verified how"  # finish, releases blocked work
bd dep add <blocked> <blocker> # <blocked> depends on <blocker>
bd remember "insight"          # store project memory
bd prime                       # reload workflow context and memory
```

Full command and dependency reference: `references/beads.md`.

## Gotchas

- **Dependency direction is easy to get backwards.** `bd dep add A B` means A depends on B, so B blocks A. At create time, `--deps depends-on:X` makes the new issue wait on X, while `--deps blocks:X` makes the new issue block X, which is the opposite. For a child under an epic, prefer `--parent` at create time. The parent-child argument order is a known source of errors, and `bd show` lists epic children under "Blocks". See `references/beads.md`.
- **The Dolt database is the source of truth, not the JSONL.** `.beads/issues.jsonl` is an export for viewers and diffs. Sync across machines with `bd dolt push` / `bd dolt pull`, not by editing the file.
- **Back up before destructive repair.** Run `bd backup` or `bd export --all` before `bd doctor --fix`. Agent-driven repair has wiped databases.
- **Do not resurrect markdown task lists.** If you catch yourself writing a TODO list in a doc, move it into beads.

## References

- `references/design-phase.md`: spec and PRD template, clarification, technical plan, decomposition into the graph.
- `references/decomposition.md`: walking skeleton, vertical slicing rules, dependency wiring, milestone exit criteria.
- `references/maintain-phase.md`: pull-queue flow, triage, prioritization, batching maintenance.
- `references/beads.md`: full `bd` command reference, dependency types, sync, and the direction gotchas.
