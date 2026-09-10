---
name: verifying-end-to-end
description: Use when a change is made and you need to prove it works for real, beyond unit tests. Covers spinning up a real environment and driving the actual surfaces the change touched, like calling backend endpoints with curl and driving the frontend with the agent-browser CLI. Trigger whenever you finish a code change and need to confirm it behaves, even when the user doesn't ask for verification.
---

# Verifying end to end

Don't rely on unit tests to verify a change works. Spin up a real environment and exercise the actual surfaces your change touched, end to end.

## How to verify

1. Start a real environment: the dev server, the service, the app.
2. Drive the exact surfaces the change affected against the real thing.
3. Confirm the observed behavior matches what the change was meant to do.
4. Show the command and its output, so the proof is visible.

## Examples

For a backend API change, call the real endpoints your change affected with `curl` and check the responses. For a frontend change, use the frontend with the `agent-browser` CLI and walk the views and flows your change affected.

## If it fails

Find the underlying cause and fix that, then run the verification again. Keep going until the real surface behaves correctly.

## Next

Once it's proven, commit and open the PR, then run `finishing-up-work` for the final checks. That skill runs this verification again as one of its steps.
