---
name: verifying-end-to-end
description: Use when a change is made and you need to prove it works for real, beyond unit tests. Covers spinning up a real environment and exercising the actual surfaces the change touched, like calling backend endpoints with curl and clicking through the frontend with the agent-browser CLI. Trigger whenever you finish a code change and need to confirm it behaves, even when the user doesn't ask for verification.
---

# Verifying end to end

Unit tests check a function in isolation. They can pass while the running system is broken, because the route isn't wired, the config is wrong, or a dependency behaves differently than the mock. So after a change, start a real environment and exercise the surfaces you touched the way a user or a caller hits them, then watch what happens.

## What counts

You have to exercise the change through a running system. A green test suite, a passing type check, or reading the code and reasoning about it don't count. Use a real server, a real database, a real browser, or a real command, and watch it do the thing.

## The loop

1. Work out which surfaces reach your change: which endpoints, screens, commands, or jobs a user or caller goes through to hit the code you edited.
2. Start a real environment with the project's own startup path: the dev server, the service with its real dependencies, the app, the database.
3. Exercise each surface for real. Cover the happy path plus the error and edge cases your change affected.
4. Check the actual result: the response and its status, the rendered screen, the exit code, and the state left in the database.
5. Paste the command and its output so the next reader can see it worked.

## Find the surfaces

Start from the diff and trace outward to what a caller touches. For a request handler, call its route. For a shared helper, exercise the callers that matter. For a migration, run it against a real database and query the result. Keep tracing until you reach a surface where the behavior is visible, not just the function you edited.

## Cover the error and edge cases

The happy path tells you the feature is there. Most of a change lives around it, so also exercise:

- Bad input, missing auth, an id that doesn't exist, a conflicting write.
- Empty lists, the first and last item, boundaries, large inputs.
- The state afterward: what got written, what got emitted, what the next request sees.

## Show the proof

Paste the real command and its real output for each path you drove: the `curl` call with its status and body, the `agent-browser` steps and what the page showed, the command and its exit code. Give enough that someone rereading can see the surface behaved.

## Surfaces and how to exercise them

- Backend API: call the real endpoints with `curl`. See `references/backend.md`.
- Frontend: work the running app with the `agent-browser` CLI. See `references/frontend.md`.
- CLI tool: run the built binary with real arguments, then check stdout, stderr, the exit code, and any files or state it changed.
- Library or module: call it from a short script or a REPL against real inputs, not only through its unit tests.
- Background job or worker: enqueue real work against the running worker and confirm it processed and left the right state.
- Database migration: run it forward against a real database, check the schema and data, then run the rollback if the project has one.

## If it fails

Good, that's the check doing its job. Find the underlying cause and fix that rather than the symptom, then run the same verification again from a clean start. If the environment won't start or the surface is hard to reach, fix that too, because you can't verify a change you can't reach.

## Reference files

- `references/backend.md`: exercising APIs with `curl`, covering status codes, headers, auth, JSON bodies, error paths, and checking the resulting state. Read it when the change touches an endpoint or a service.
- `references/frontend.md`: working a running app with the `agent-browser` CLI, covering the read-act-verify loop, refs and selectors, assertions, and capturing what the page showed. Read it when the change touches the UI.

## Next

Once the change is proven on every surface it touched, commit and open the PR, then run `finishing-up-work` for the final checks. That skill runs this verification again as one of its steps, so leave the environment in a state you can bring back up.
