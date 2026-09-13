---
name: writing-agents-md
description: Write or update an AGENTS.md file for a project so coding agents have the context and instructions they need. Use when starting a new project, adding an AGENTS.md, or improving an existing one, even when the user doesn't say "AGENTS.md" directly.
---

# Writing AGENTS.md

AGENTS.md is a plain Markdown file at the repo root holding the context and commands an agent needs. It follows the open format at https://agents.md, so one file works across many agents.

Keep it short: the project in a line or two, then only what an agent can't work out by exploring the repo.

## When to write one

- Starting a new project, alongside the linting and formatting setup.
- The repo has commands, conventions, or gotchas an agent would miss.
- An existing one is stale. Keep it current as the project moves.

## Where it goes

One file at the repo root covers the project. In a large monorepo, add a nested one inside each package, since agents read the nearest file in the tree.

## What to include

Only the sections that help:

- A one line description of what the project is.
- Setup and dev commands, like installing deps and starting a dev server.
- The exact build, lint, typecheck, and test commands, since agents run them and fix failures before finishing.
- Conventions a formatter or linter config doesn't already enforce.
- Anything you'd tell a new teammate: commit or PR rules, security gotchas, deployment steps, how to publish a release.

## What to leave out

- Rules that live in global agent instructions.
- Anything an agent can learn by reading the code or config.
- Long prose. Use short sections, lists, and fenced command blocks, in plain sentences with real command and tool names.

## Example

````markdown
# my-project

A CLI that renames things.

## Verifying changes

```sh
pnpm run format
pnpm run typecheck
pnpm run lint
pnpm test
```

## Contributing

1. Work on a feature branch and run `pnpm run check`.
2. Open a PR into `main` and wait for CI to pass before merging.
````
