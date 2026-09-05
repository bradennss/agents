# Task ledger formats

The exact setup for the two ledger options, plus the task format, progress log, and smoke-test convention. Read the option you picked in the SKILL.md.

## Option A: Beads (`bd`)

Beads is a graph-based issue tracker for coding agents. It stores issues as JSONL in git, so the ledger syncs across machines and multiple agents share one logical database. It gives you stable hash IDs that don't collide on merge, four dependency types (blocks, related, parent-child, discovered-from), and ready-work detection that finds issues with no open blockers.

It's alpha and the command set changes, so don't run commands from memory. Get the current setup and the live command list from the tool itself.

Install one way:

```bash
# npm (works in most agent environments)
npm install -g @beads/bd

# or macOS / Linux
curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash

# or Homebrew
brew tap steveyegge/beads && brew install bd
```

Set it up once in the repo root:

```bash
bd init          # creates .beads/, imports any existing issues, offers git hooks
bd onboard       # prints the current agent integration steps and command list
```

`bd onboard` is the source of truth for how to use it: it writes the workflow into `AGENTS.md` and tells you the exact subcommands for creating issues, adding dependencies, querying ready work, and closing. Run `bd --help` if a command errors, and re-read the output instead of guessing at flags.

What to lean on once it's set up:

- Create one issue per small task, and set dependencies so blocked work can't surface as ready.
- Ask for ready work at the start of a session to pick what to do next.
- When you discover new work mid-task, file it and link it with `discovered-from` so the audit trail shows where it came from.
- Use epics (parent-child) to group the tasks of a larger feature under one umbrella.

For protected `main` branches, initialize with a separate metadata branch (`bd init --branch beads-metadata`). For multiple agents or worktrees, Beads reconciles through git, so this is the option to pick when more than one writer is in play.

## Option B: file-based ledger

No install, no dependency. Good for small or self-contained projects. You give up automatic ready-work detection and dependency queries, so you track those by hand.

Keep the machine-edited status as JSON. Agents rewrite or delete Markdown entries far more readily than JSON, and a lost task is the whole failure this system prevents.

`tasks.json` at the repo root:

```json
{
  "tasks": [
    {
      "id": "T1010",
      "title": "User can sign up with email and password",
      "depends_on": [],
      "parallel_safe": true,
      "scope": ["src/auth/signup.ts", "tests/auth/signup.test.ts"],
      "done_when": [
        "valid email and password creates an account",
        "duplicate email is rejected with a clear error",
        "targeted tests and the full suite pass"
      ],
      "status": "open"
    },
    {
      "id": "T1020",
      "title": "User can log in and get a session",
      "depends_on": ["T1010"],
      "parallel_safe": false,
      "scope": ["src/auth/login.ts"],
      "done_when": [
        "correct credentials return a session",
        "wrong password is rejected"
      ],
      "status": "open"
    }
  ]
}
```

Rules for the fields:

- `id`: stable, never reused. Prefix plus a number (`T1010`) so commits, PRs, and progress notes can point at the same task. Leave gaps (10, 20) so you can slot work in between.
- `depends_on`: IDs that must be done first. A task is ready when every ID here is done.
- `parallel_safe`: true only when the task's files don't overlap another ready task's files. Used when handing slices to subagents.
- `done_when`: observable checks, not "implementation complete". This is what "done means verified" grades against.
- `status`: `open`, `in_progress`, `done`, or `blocked`. Flip to `done` only after the `done_when` checks pass for real.

If you'd rather read the backlog as prose, `TASKS.md` works too, but keep the same fields:

```markdown
- [ ] T1010 User can sign up with email and password
  - depends on: none
  - scope: src/auth/signup.ts, tests/auth/signup.test.ts
  - done when: valid signup creates an account; duplicate email is rejected; tests pass
```

Trade-off: Markdown is easier for a human to skim, but agents edit it loosely, so prefer JSON when the agent is the main writer.

## The progress log

`PROGRESS.md` at the repo root, newest entry on top, one short entry per session. This is the narrative git can't show: why you did it and what's next.

```markdown
# Progress log

## 2025-06-12, session 7

Done: T1020 login, verified end to end (logged in through the browser, session cookie set). Why: needed before the profile page (T1030) can read the current user. Left: T1030 profile page, T1040 password reset. Next: start T1030. The user object is on `req.session.user`. Blockers: none.

## 2025-06-11, session 6

Done: T1010 signup. Filed T1050 (rate-limit the signup endpoint) as discovered-from T1010. Next: T1020 login.
```

Keep recent entries in full. Once work is merged and captured in git, trim old entries to a single line so the file doesn't grow forever.

## The smoke test

The session-start check needs a one-command way to run the project. Have the setup create it:

- An `init.sh` that installs deps and starts the dev server, or a documented `make dev` / `npm run dev`.
- A short note in `AGENTS.md` of the one path that proves the app is alive (for a web app, load the page and do the core action; for an API, hit the main endpoint; for a CLI, run it on real input).

Run it at the start of every session before new work. If it's broken, the last session left a mess, so fix that first.
