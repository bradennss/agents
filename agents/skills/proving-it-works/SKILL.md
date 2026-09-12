---
name: proving-it-works
description: Verify a change by running it in a real environment and driving it end to end. Use when a change is written and needs checking, when unit tests pass but nothing has been run, before telling the user that something works, or when an endpoint, a view, or a flow needs exercising.
---

# Proving it works

Passing unit tests doesn't verify that a change works. Spin up a real environment and drive the change end to end.

## Start the environment

Follow the `setting-up-dev-environment` skill whenever you start something that listens on a port or holds state, so you can't collide with another checkout running the same stack.

## Drive the change

- Backend: call the affected endpoints with curl.
- Frontend: exercise the affected views and flows with the `agent-browser` CLI.

## Report it

Show the commands you ran and what they printed, not just the verdict.
