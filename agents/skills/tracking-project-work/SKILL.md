---
name: tracking-project-work
description: >
  Set up and run durable task tracking for a project that spans many sessions, so work survives context resets and each session picks up where the last one left off. Use when starting or maintaining a large, long-lived project, when the user wants a task tracker, backlog, issue system, or project "memory", when an agent keeps forgetting what's done between sessions, or when you need a progress log and a ledger of what's left, even if the user just says "help me keep track of this build".
---

# Tracking project work

A big project runs across many sessions, and each new session starts with no memory of the last one. Whatever lived only in the chat is gone. So keep the project's state in durable files that the next session can read cold, and run each session like a shift handoff.

This skill sets up two things and then uses them every session:

- A **task ledger**: what's left to do, with stable IDs, dependencies, and a done-when line per task.
- A **progress log**: what happened, why, and the next step, updated at the end of every session.

Git already covers what changed, and `AGENTS.md` plus docs cover how the system works. You're adding the two pieces git and docs don't: the backlog and the running narrative.

## When to set this up

Set it up for a project that will outlive a single session: a new app you're building over days, a big migration, a long investigation, or a codebase you'll maintain over time. Skip it for a one-off fix or a small script, where a plan in chat is enough.

## Step 1: pick the ledger tool

Two good options. Pick one and don't run both.

**Beads (`bd`) for real, ongoing projects.** It's a graph-based issue tracker built for coding agents: stable IDs, four dependency types, ready-work detection, and a git-backed store that syncs across machines and multiple agents. Use it when the project is large, will run for a while, or has more than one agent working on it. It's alpha, so read the current docs before setup because commands change.

**A file-based ledger for small or self-contained projects.** A single `tasks.json` (or `TASKS.md`) in the repo, no install, no dependency. Use it when the project is modest or you want zero setup. You lose automatic ready-work detection and dependency queries, but it's plain and portable.

Both formats, the exact Beads commands, and the file schemas live in [references/task-ledger.md](references/task-ledger.md). Read it now to do the setup.

## Step 2: scaffold the state

Once you've picked a tool, create the durable files. At a minimum:

- The ledger (a `bd` database, or `tasks.json` / `TASKS.md`), seeded with the known work broken into small, verifiable tasks.
- `PROGRESS.md` at the repo root: a running log, newest entry on top, one short entry per session.
- A one-command way to run the project and smoke-test it, like an `init.sh` or a documented `make dev` / `npm run dev`. The session-start check depends on this.

Then wire it into `AGENTS.md` so every future session knows the system exists and how to use it. Add a short note like "Track work in `tasks.json` and log every session in `PROGRESS.md`; read both before starting" (or the `bd onboard` line if you chose Beads). Follow the `writing-agents-md` skill for that edit.

Break the seeded work into small tasks, each a thin vertical slice that works end to end ("signup works", not "build all the tables"). Give each one a stable ID, its dependencies, and a done-when line. The task format is in the reference.

## Step 3: run every session as a handoff

This is the loop every later session follows. It's the same loop whether you chose Beads or files.

1. **Get your bearings.** Read the top of `PROGRESS.md` and the recent git log. Ask the ledger for ready work (`bd ready`, or read the open tasks in the file). Run the smoke test to catch anything the last session left broken, and fix that before touching new work.
2. **Pick one task.** Take the highest-priority task that isn't blocked. Work just that. Don't one-shot the whole project; that's the failure mode this whole system exists to prevent.
3. **Verify it for real.** Follow the "Done means verified" rule from the global `AGENTS.md`: run the real path, not just unit tests. Only flip a task to done when a test or recorded evidence backs it.
4. **Commit and hand off.** Commit with a message that names the task ID. Update the ledger (close the task, add any new work you found). Write a `PROGRESS.md` entry: what you did, why, what's left, and the next step. Leave the repo clean enough to merge.

## Rules that keep it honest

- **File discovered work, don't drop it or chase it.** When you notice a bug or a follow-up mid-task, add it to the ledger and keep going. In Beads, link it with the `discovered-from` dependency so the trail is clear.
- **Never mark done on a hunch.** "The code looks right" isn't evidence. A passing end-to-end check is.
- **Leave a clean state.** End every session with code a teammate could merge: no half-finished feature, no undocumented breakage. If you can't finish a task, revert to the last working commit or clearly mark it in progress in `PROGRESS.md`.
- **Keep the machine-edited status file as JSON, not Markdown.** Agents are much less likely to quietly rewrite or delete entries in a JSON file. Use JSON for a file-based ledger's status and keep prose notes in `PROGRESS.md`.
- **One writer per file.** If several agents run at once, give each its own branch or worktree and let the ledger reconcile. Beads is built for this; a shared `tasks.json` edited by two agents at once will collide.

## Gotchas

- Compaction drops detail. Don't rely on the model "remembering" a decision from earlier in the session; if it matters next session, it goes in `PROGRESS.md` or an ADR, not just the chat.
- A later session will look around, see progress, and want to declare the project done. The ledger is the guard: the project is done when the ledger is empty of open tasks and each is verified, not when the app looks finished.
- Beads is alpha. Pin to what you installed, and re-check the docs if a command errors instead of guessing at flags.
- Don't let `PROGRESS.md` grow forever. Keep recent entries in full and trim old ones to a line once their work is merged and captured in git.
