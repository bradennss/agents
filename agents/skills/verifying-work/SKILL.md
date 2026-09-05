---
name: verifying-work
description: >
  Verify that a change actually works before you call it done, and check yourself against the rules while you work. Use when you're about to say something is done, works, is fixed, or is ready, or when you're reviewing your own output or a subagent's result, even if you feel sure it's right.
---

# Verifying work

Code that compiles isn't done, and passing the unit tests isn't either. It's done when you've run the real thing in a real environment and watched it behave. Being sure isn't proof.

## Run the real thing

Match the check to the change:

- Backend change: start the service, call every endpoint you touched, and check the status, body, and logs.
- Frontend change: start the app and drive it with `agent-browser`, installing it if it isn't there. Click the path a real user would click, and check the console and the network traffic.
- CLI or script: run it on real input.
- Migration or data change: run it on a copy, then read the rows back.
- Config or infra change: apply it somewhere safe and check the effect.

Run the whole path, including the error case you just added. Put the real commands and their output in your summary so a reviewer can see it ran. If you couldn't check something, say which part and why, and don't call it done just because the code looks right.

## Check yourself against the rules too

Rereading your own output and asking "does this look right?" doesn't work. You can fix a mistake once someone points at it, but you're bad at spotting it yourself, and going by feel can leave the work worse than before. So check against something outside your head, and turn every rule into a check that passes or fails:

- Style: grep the file for the words and patterns the Voice rules ban.
- Code: run the linter, type checker, and tests.
- Facts and numbers: look them up, or work them out again with a tool.
- Plan: read your own step list and point at which step you're on.

## How to run a check

1. Say which command proves the claim.
2. Run the whole command fresh.
3. Read the output and the exit code.
4. State the result with the output next to it.

Run the check in the same message where you say the code works, because a check from a few edits ago won't tell you if it still works now. Fix what the check finds, then run it again, and keep going until it comes back clean.

## Stop and run the check

When you catch yourself about to say one of these, run the check instead:

| What you're about to say | What to do |
| --- | --- |
| "It should work now" | Run it. |
| "I'm confident this is right" | Being sure isn't proof. Run it. |
| "The linter passed" | The linter isn't the tests. Run both. |
| "The subagent reported success" | Read the diff yourself. |
| "It's a small change" | Small stuff breaks too. Run it. |
| "This rule doesn't really apply here" | It does. Follow it. |
