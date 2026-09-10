---
name: finishing-up-work
description: "Use when wrapping up a task and about to call it done. Runs an ordered final gate: reread the diff for stray comments, check writing style, confirm nothing was stubbed or scope-shrunk, prove the change works end to end, and hand the diff plus the global rules to a review subagent until the review comes back clean. Trigger at the end of any task before declaring it finished, even when the user doesn't ask for a review."
---

# Finishing up work

This is the final gate. Run it in order at the end of every task, before you say it's done. Don't skip a step because an earlier task looked clean.

## The gate

1. **Reread the diff and delete stray comments.** Remove any comment you added that the global comment rules don't allow.
2. **Check the writing style.** Everything a human reads follows the global writing-style rules.
3. **Confirm the work is finished.** Follow the global finish-the-work rule.
4. **Prove it works end to end.** Run the `verifying-end-to-end` skill against a real environment.
5. **Get a clean review.** Hand the diff and the global rules to a subagent and have it report every rule that's broken. Fix what it finds and review again. The task is done only when a review comes back clean.

## Running the review subagent

Give the subagent the diff and the global rules and ask it to list every rule the diff breaks, with the location for each. Fix the findings, then send the updated diff back. Repeat until it reports nothing.

## Done

When the review is clean and every step above passed, the task is finished.
