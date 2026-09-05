---
name: project-setup
description: >
  Get a project's baseline in place: an AGENTS.md, modern tooling, strict formatting, strict linting, and CI that runs them. Use the first time you touch a project, whether you're creating it fresh or onboarding one that already exists, and whenever the AGENTS.md needs creating or updating, even if the user just says "set this up" or "get me started here."
---

# Project setup

Do this the first time you touch a project, whether you're creating it fresh or onboarding one that already exists. Get the baseline in place before you write real code, so every project runs the same way and the next agent knows where things live. If a piece is already there and working, leave it and move on.

## The baseline

Four things:

- **An AGENTS.md.** Every project gets one. See "Keep AGENTS.md current" below.
- **Modern tooling.** Pick the current standard tools for the ecosystem, not the ones you used years ago. For anything JS or TS, use pnpm instead of npm or yarn, and pin the version with the `packageManager` field in `package.json`. Look up the current version instead of guessing.
- **Strict formatting.** Add a formatter with its config committed, so layout is automatic and nobody argues about it. For JS/TS that's Prettier or Biome. Add a `format` script that writes changes and a `format:check` script that only checks.
- **Strict linting.** Add a linter in its strict mode with the config committed. For JS/TS that's ESLint with a type-checked config, or Biome. Turn the strict rules on from the start, since it's much harder to add them to a big codebase later. Add a `lint` script.

Then wire it up so it actually runs:

- Put the real commands in `package.json` scripts (or the ecosystem's equivalent) and write them into AGENTS.md so the next agent finds them.
- Run the format check and the linter in CI, so a bad push fails.
- Turn on strict type checking where the language has it, like `strict: true` in `tsconfig.json`.

## Onboarding an existing project

Same list, but look at what's there first. Match the tools the repo already uses instead of swapping them out mid-project, and only add the missing pieces. If it already uses npm with a committed lockfile, don't switch it to pnpm without asking.

## Keep AGENTS.md current

Every project needs an AGENTS.md, the README for the next agent. If one is missing, create it. If one's there but stale or thin, update it as you learn how the project really works.

Write down what you had to figure out to get moving: the setup commands, how to run the app, how to run the tests, and the conventions the repo already follows. If you hit a gotcha and wished it was written down, that's the thing to add.

- Do it as you go. When you learn the test command or a build step, add it right then, not at the end.
- Keep it short and true. A rule that's out of date is worse than no rule, so fix or drop anything that no longer holds.
- Don't repeat the global rules in a project's file. Cover only what's specific to that project.

For how to actually write the file, use the `writing-agents-md` skill.
