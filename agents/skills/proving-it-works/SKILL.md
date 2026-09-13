---
name: proving-it-works
description: Verify a change by running it in a real environment and driving it end to end. Use when a change is written and needs checking, when unit tests pass but nothing has been run, before telling the user that something works, or when an endpoint, a view, or a flow needs exercising.
---

# Proving it works

Unit tests passing doesn't show the change works. Start a real environment and drive it end to end.

## Start the environment

Anything that listens on a port or holds state takes its ports and resource names from `setting-up-dev-environment`, so it can't collide with another checkout.

## Drive the change

- Backend: call the affected endpoints with curl.
- Frontend: exercise the affected views and flows with the `agent-browser` CLI.
- Library or CLI: call it from the shell or a scratch script, on real input.
- Migration: run it forward against data in the old shape, from the repo's fixtures or a copy of real rows, then query the rows and show them.
- Docs, config, and anything with nothing to run: run the repo's own checks over it, like the formatter, the linter, or the schema validator.

Show the commands and what they printed, not only the verdict.

## When it runs

- Autonomous mode: before the review, except the trivial short path and tasks that end in findings.
- Collaborative mode: drive the round you just finished before its check-in, and run the whole change among the closing steps when the user picks it. A design pass shows screenshots instead, so the end to end run waits for the last pass.
