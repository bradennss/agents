---
name: reviewing-your-work
description: "Judge a finished change by handing the whole session diff to a reviewer with fresh context, fixing everything it finds, and repeating until a review comes back clean. Use when a change is written and about to be called done, when a task is closing and the diff needs judging against the instructions, or when the user asks for a review of the work."
---

# Reviewing your work

The agent that wrote the code is the worst judge of it. A reviewer with fresh context reads the diff cold and tells you what's wrong.

Use the repo's own default branch name in place of `<default-branch>`.

## Write the diff

Committed and uncommitted work go in one file:

```sh
git add -N .
git diff $(git merge-base HEAD <default-branch>) > /tmp/<branch>-review.diff
```

`git add -N .` puts new files in the diff without staging their contents. Work done in place diffs against the commit the task started from.

## Hand it to a fresh reviewer

One subagent, fresh context, following the `delegating-to-subagents` skill. Its brief:

- Read the diff file, by path.
- Read every instruction that applies, global and project.
- Judge every line of the diff against them, with maximum scrutiny.
- Report every issue found, with the file and the line.
- Fix nothing.

Don't tell it what you think is risky. A steer narrows what it looks at.

## Fix, then review again

Fix everything it reports. An issue you disagree with gets a reason in your reply, not silence.

Then write the diff again and run another reviewer with fresh context on the new diff. The work is done when a review comes back with nothing.

## When it runs

- Autonomous mode: every task, before committing, looping until clean.
- Collaborative mode: when the user picks it among the closing steps.
