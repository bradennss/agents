---
name: reviewing-your-work
description: Judge a finished change by handing the diff to reviewers with fresh context, fixing what blocks, and stopping after at most three rounds. Use when a change is written and about to be called done, when a task is closing and the diff needs judging against the instructions, or when the user asks for a review of the work.
---

# Reviewing your work

You already believe your own change is right, which is why someone else judges it. A reviewer with fresh context reads the diff cold and tells you what's wrong.

Rounds are what a review costs, so the loop stops after three. A loop with no stopping rule finds work forever: every fix gives the next round more to pick at, and a reviewer with no fixed target widens what it looks at until something turns up.

## Run the repo's own checks first

Run the formatter, the linter, the type checker, and the tests before you write a diff, every time you write one. A round spent on formatting is a round you paid for nothing, and a tool answers that question for free.

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

## What blocks

A finding blocks when the change is wrong without the fix: behavior that breaks, an instruction the diff breaks, part of the task that isn't done, a caller left behind.

Everything else doesn't block: a better name, a clearer sentence, a refactor worth doing, polish. Style and taste never block, whatever a reviewer calls them, and that covers the writing rules in the instructions.

## Round one: three reviewers at once

Three subagents, fresh context, running in parallel, following the `delegating-to-subagents` skill. Running them together costs the wall clock of one, and a single reviewer finds a slice of what's there and leaves the rest for later rounds.

Each lane gets its own question and the list of what it leaves to the others, otherwise you get the same finding three times:

- Correctness. Whether the change does what the task set out, across the edge cases, the error paths, the data, and the callers. Leaves style and wording alone, and leaves what's missing or out of scope to the completeness lane.
- Instructions. Every rule in the instructions that applies, global and project, including the ones about writing and structure. Hands whether the code works to the correctness lane, and what's missing to the completeness lane.
- Completeness. Anything stubbed, half migrated, left dead, or outside what the task agreed. Ignores style, and doesn't judge whether the logic is right.

Drop a lane when the change holds nothing for it, like the correctness lane on a diff with no code in it.

Every lane gets the same brief around its question:

- Read the diff file, by path.
- Read every instruction that applies, global and project.
- Judge the diff against them, with the file and the line for each finding, and the evidence behind it.
- Mark each finding blocking or not, against the test above. Put that test in the brief in full, since a reviewer with fresh context has no other way to know where the floor is.
- Fix nothing, and in round one, stay out of the other lanes.

Give them what the task agreed to do, meaning what done means and what's out of scope. Autonomous mode settles that in its question pass, and collaborative mode in what the user picked along the way. Without it, each round is free to move the target, and work nobody asked for comes back as findings.

Don't tell a reviewer what you think is risky, and don't hand it the story of the work. A steer narrows what it looks at, and a reviewer holding the previous critique audits your fixes and inherits the previous reviewer's blind spots. A reviewer reading this skill doesn't run a reviewer of its own.

## Fix, then round two

Nothing blocking in any lane means the review is done. Most changes stop here.

Otherwise fix everything that blocks. A finding you disagree with gets a reason in your reply, and it's settled: it goes to the next reviewer as a decision already taken, and it can't block a later round without new evidence. Round two gets those decisions and nothing else about the work so far, so no round numbers and no list of what you fixed.

A finding in code your diff never touched is a bug your change runs into, which you fix in its own commit, or unrelated work, which you report rather than quietly take on.

Then write the diff again and hand the whole thing to one reviewer with fresh context, under the same brief with no lane and no exclusion list. Its question is whether anything still blocks, and nothing blocking ends the review.

## Round three is the last one

When round two blocks, fix it, write the diff again, and hand it to one reviewer with fresh context, under the same brief, along with what round two found. This is the one round that sees the previous findings, because its question is narrow: whether each of them is cleared, and whether the fixes broke anything around them. Fixes are where new defects come from, so this round exists to catch them.

Then stop, whatever it says. Fix what blocks, and put anything still open at the top of your report. Past three rounds the loop trades one finding for another instead of closing.

## Sweep the rest

Once the last round is done, take one pass at the non-blocking findings from every round and fix what's cheap. That pass goes to nobody for review, so run the repo's checks over it before you commit. List what you left, and why, in your report.

## When it runs

- Autonomous mode: before committing, on every task except the short path the `working-autonomously` skill gives trivial work, and tasks that end in findings rather than a diff.
- Collaborative mode: when the user picks it among the closing steps.
