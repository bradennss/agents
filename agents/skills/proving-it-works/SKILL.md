---
name: proving-it-works
description: Verify a change by running it in a real environment and driving it end to end. Use when a change is written and needs checking, when unit tests pass but nothing has been run, before telling the user that something works, or when an endpoint, a view, or a flow needs exercising.
---

# Proving it works

Unit tests passing doesn't show that the change works. Start a real environment and drive the change end to end.

## Start the environment

Anything that listens on a port or holds state goes through the `setting-up-dev-environment` skill, so you can't collide with another checkout running the same stack.

## Drive the change

- Backend: call the affected endpoints with curl.
- Frontend: exercise the affected views and flows with the `agent-browser` CLI.

## Report it

Show the commands you ran and what they printed, not only the verdict.
