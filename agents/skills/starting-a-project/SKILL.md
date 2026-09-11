---
name: starting-a-project
description: Set up a new project with a formatter, a strict linter with warnings as errors, a type checker, tests, a pre-commit hook, CI, and an AGENTS.md. Use when creating a new repo or package, scaffolding a service or library, bootstrapping a codebase from scratch, or adding this baseline to a project that's missing it.
---

# Starting a project

Set up the tooling before you write features, so the formatter, the linter, the type checker, and the tests run in a hook and in CI.

Build in place, following the `git-worktrees` skill.

## What to set up

- A formatter, with its config committed.
- A linter on its strictest settings, like clippy pedantic, with warnings as errors.
- A type checker, where the language has one.
- A test runner, with at least one real test.
- A pre-commit hook that runs the formatter, the linter, the type checker, and the tests.
- A CI workflow that runs those same four, so a skipped hook still gets caught.
- An `AGENTS.md`, written with the `writing-agents-md` skill, and a `CLAUDE.md` symlink pointing at it. Delete any `CLAUDE.md` that's already there.
