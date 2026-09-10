---
name: setting-up-a-project
description: Use when starting a new or greenfield project and it needs its baseline set up. Covers strict linting with warnings as errors, formatters, a pre-commit hook and CI that run format, lint, typecheck, and tests, then an AGENTS.md and a CLAUDE.md symlink. Trigger on prompts like "start a new project", "set up this repo", "bootstrap a service", or "scaffold a new package", even when the user doesn't list these steps.
---

# Setting up a project

A new project needs its rules enforced mechanically from the first commit, not just written in prose. Set up the baseline so the formatter, linter, type checker, and tests run on every commit and in CI.

## Steps

1. **Linting** with strict settings, like clippy pedantic, and warnings treated as errors.
2. **Formatters** wired up for the language.
3. **A pre-commit hook and CI** that both run the formatter, the linter with warnings as errors, the type checker, and the tests. The hook catches it locally, CI enforces it for everyone.
4. **AGENTS.md** at the repo root.
5. **CLAUDE.md symlink** pointing at AGENTS.md. If a CLAUDE.md already exists, delete it first, then create the symlink.

## Writing the AGENTS.md

Don't write it from scratch here. Use the `writing-agents-md` skill for how to write one. Keep it simple and concise: briefly describe the project and add only what exploring the repo can't tell an agent, like the exact verify commands. Don't repeat the global rules.

## Next

Once the baseline is in and green, move into building. After the build works, `verifying-end-to-end` confirms it runs for real. Then commit and open a PR. When you finish, `finishing-up-work` runs the final gate.
