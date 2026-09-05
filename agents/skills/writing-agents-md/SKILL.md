---
name: writing-agents-md
description: Write or update an AGENTS.md file for a project so coding agents have the context and instructions they need. Use when starting a new project, adding an AGENTS.md, or improving an existing one, even when the user doesn't say "AGENTS.md" directly.
---

# Writing AGENTS.md

AGENTS.md is a README for agents. It's a plain Markdown file at the repo root that gives coding agents the context and commands they need to work on the project. It follows the open format at https://agents.md, so one file works across many agents.

Keep it simple and concise. It should briefly describe the project and add only what exploring the repo can't tell an agent on its own. Don't restate things the code, config, or file layout already make obvious.

## When to write one

- Starting a new project. Add it alongside linting, formatting, and other setup.
- The repo has commands, conventions, or gotchas an agent would otherwise miss.
- An existing AGENTS.md is stale. Treat it as living documentation and keep it current as the project changes.

## Where it goes

- One AGENTS.md at the repo root covers the whole project.
- For a large monorepo, add a nested AGENTS.md inside each package. Agents read the nearest file in the tree, so the closest one wins.

## What to include

Add only the sections that help. Common ones:

- A one line description of what the project is.
- Setup and dev commands, like installing deps and starting a dev server.
- How to verify changes: build, lint, typecheck, and test commands. Agents run these and fix failures before finishing, so list the exact commands.
- Code style and conventions that aren't already enforced by a formatter or linter config.
- Anything you'd tell a new teammate: commit or PR rules, security gotchas, deployment steps, how to publish a release.

## What to leave out

- Rules that live in global agent instructions. Don't repeat them per project.
- Anything an agent can learn by reading the code or config.
- Long prose. Prefer short sections, lists, and fenced command blocks.

## Writing style

Write plain, simple sentences a new teammate could follow. Use real command and tool names. Skip filler and marketing language.

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
