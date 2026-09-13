---
name: reviewing-your-work
description: Judge a finished change by handing the whole session diff to a reviewer with fresh context, fixing everything it finds, and repeating until a review comes back clean. Use when a change is written and about to be called done, when a task is closing and the diff needs judging against the instructions, or when the user asks for a review of the work.
---

# Reviewing your work

You already believe your own change is right, which is why someone else judges it. A reviewer with fresh context reads the diff cold and tells you what's wrong.

## Write the diff

Committed and uncommitted work go in one file, diffed against the commit the work started from, which the `setting-up-dev-environment` skill noted at setup. That's the commit the branch was created at or last rebased onto, or the tip of the checkout for work done in place:

```sh
git add -N .
git diff <starting-commit> > /tmp/<branch>-review.diff
```

Using the recorded commit rather than a merge base keeps other people's upstream commits out of the file, and still works when the branch started from a local default branch holding unpushed commits.

A repo with no commits yet has no starting commit, so review the whole tree:

```sh
git add -N .
git diff $(git hash-object -t tree /dev/null) > /tmp/<branch>-review.diff
```

`git add -N .` puts new files in the diff without staging their contents.

## Hand it to a fresh reviewer

One subagent, fresh context, following the `delegating-to-subagents` skill. Its brief:

- Read the diff file, by path.
- Read every instruction that applies, global and project.
- Judge every line of the diff against them, with maximum scrutiny.
- Report every issue found, with the file and the line.
- Fix nothing.

Don't tell it what you think is risky. A steer narrows what it looks at. A reviewer reading this skill doesn't run a reviewer of its own.

## Fix, then review again

Fix everything it reports. An issue you disagree with gets a reason in your reply, not silence, and goes into the next reviewer's brief as already decided, so a fresh reviewer doesn't raise it again and the loop can still close.

A finding in code your diff never touched is a bug your change runs into, which you fix in its own commit, or unrelated work, which you report rather than quietly take on.

Then write the diff again and run another reviewer with fresh context on the new diff. The work is done when a review comes back with nothing outstanding.

## When it runs

- Autonomous mode: before committing, looping until clean, on every task except the short path the `working-autonomously` skill gives trivial work, and tasks that end in findings rather than a diff.
- Collaborative mode: when the user picks it among the closing steps.
