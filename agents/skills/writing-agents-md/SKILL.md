---
name: writing-agents-md
description: >
  Write or improve an AGENTS.md file, the README for coding agents that gives them setup commands, tests, and project conventions. Use when the user wants to add, scaffold, fix, or restructure an AGENTS.md (or migrate an old AGENT.md, CLAUDE.md, or .cursorrules into one), even if they just say "give the agent project instructions" without naming the file.
---

# Writing AGENTS.md

AGENTS.md is a plain Markdown file at the repo root that tells a coding agent how to work on the project. Think of it as a README for agents: the build steps, test commands, and conventions you'd tell a new teammate, kept out of the README so human docs stay clean. It's an open format used across many agents (Codex, Cursor, Jules, Amp, Factory, and more), so one file works everywhere.

## When to write one

Write an AGENTS.md when a project has setup steps, test commands, or conventions an agent would otherwise guess at. Skip it for a repo so small there's nothing to say beyond the README.

Before writing, look at what the project already has. Don't invent rules from thin air.

- Read the README, `package.json` scripts, CI config (`.github/workflows/`), and any lint or format config.
- Pull the real commands from there: how to install, build, test, and lint.
- Match the conventions the code already follows instead of imposing new ones.

## The format

There are no required fields and no fixed schema. It's standard Markdown, so use any headings you like. The agent just reads the text.

Keep it short and specific. An agent reads the whole file every time, so cut anything it would already know (what TypeScript is, how git works) and keep the parts unique to this project.

## Sections worth including

Add a section only when you have something concrete to put in it. Common ones:

- **Project overview** — a couple of sentences on what the project is and how it's laid out, only if the structure isn't obvious.
- **Setup / dev environment** — the exact commands to install deps and start the dev server.
- **Build and test commands** — how to run the full suite, and how to run a single test. Agents run these to check their own work, so give the real commands.
- **Code style** — the conventions that aren't already enforced by a formatter, like "functional patterns where possible" or naming rules.
- **Testing instructions** — where CI is defined, what has to pass before merge, and to add or update tests for changed code.
- **Commit and PR guidelines** — subject line format, PR title format, checks to run before committing.
- **Security** — gotchas like secrets that must never be committed, or endpoints that need extra care.

Write commands as exact, copy-pasteable lines. An agent will run the test and lint commands you list and try to fix failures before finishing, so make sure they actually work.

## Example

```markdown
# AGENTS.md

## Setup commands

- Install deps: `pnpm install`
- Start dev server: `pnpm dev`
- Run tests: `pnpm test`

## Code style

- TypeScript strict mode
- Single quotes, no semicolons
- Use functional patterns where possible

## Testing instructions

- CI lives in `.github/workflows`.
- Run `pnpm test` and fix failures before merging.
- Run one test with `pnpm vitest run -t "<test name>"`.
- Add or update tests for the code you change.

## PR instructions

- Title format: `[<package>] <Title>`
- Run `pnpm lint` and `pnpm test` before committing.
```

## Monorepos: nest the files

In a monorepo, put an AGENTS.md at the root and another inside each package that needs its own rules. Agents read the nearest file in the directory tree, so the closest one wins. Keep the root file to shared rules and let each package file cover only what's specific to it. Big repos run this at scale (OpenAI's main repo has dozens of AGENTS.md files).

## Precedence

When rules conflict, the AGENTS.md closest to the edited file wins over ones higher up. An explicit instruction from the user in chat overrides everything in the file.

## Migrating existing docs

If the project already has agent instructions under another name (`AGENT.md`, `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`), move the content into AGENTS.md. To keep the old path working for a tool that still expects it, add a symlink:

```bash
mv AGENT.md AGENTS.md && ln -s AGENTS.md AGENT.md
```

Some agents read AGENTS.md only after a small config change:

- Aider: add `read: AGENTS.md` to `.aider.conf.yml`.
- Gemini CLI: set `{ "context": { "fileName": "AGENTS.md" } }` in `.gemini/settings.json`.

## Check it before you finish

- Run every command you listed and confirm it works from a clean checkout.
- Reread the file and cut any line the agent would already know without being told.
- Confirm it's named `AGENTS.md` and sits at the repo root (plus each package root for a monorepo).
