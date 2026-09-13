---
name: reviewing-your-work
description: Judge a finished change by handing the diff to reviewers with fresh context, fixing what blocks, and stopping after at most three rounds. Use when a change is written and about to be called done, when a task is closing and the diff needs judging against the instructions, or when the user asks for a review of the work.
---

# Reviewing your work

Reviewers with fresh context judge the diff, since you already believe your own change is right. At most three rounds, because a loop with no stopping rule finds work forever.

## Before each round

- [ ] Run the repo's formatter, linter, type checker, and tests. A round spent on formatting is a round paid for nothing.
- [ ] Write the diff against the commit noted at setup, which is where the branch started or last rebased onto, or the tip of the checkout for work done in place:

```sh
git add -N .
git diff <starting-commit> > /tmp/<branch>-review.diff
```

`git add -N .` puts new files in the diff without staging their contents. The noted commit keeps other people's upstream commits out, and still works when the branch started from a local default branch holding unpushed commits. A repo with no commits yet diffs against `$(git hash-object -t tree /dev/null)` instead.

## What blocks

- Blocks: behavior that breaks, an instruction the diff breaks, part of the task undone, a caller left behind.
- Doesn't block: a better name, a clearer sentence, a refactor worth doing, polish. Style and taste never block, whatever a reviewer calls them, including the writing rules.

## Round one: three reviewers at once

Three subagents with fresh context, in parallel, following `delegating-to-subagents`. Each lane gets its own question and the list of what it leaves to the others, otherwise the same finding comes back three times.

| Lane | Judges | Leaves alone |
| --- | --- | --- |
| Correctness | whether the change does what the task set out, across edge cases, error paths, data, and callers | style and wording, what's missing |
| Instructions | every rule that applies, global and project, including writing and structure | whether the code works, what's missing |
| Completeness | anything stubbed, half migrated, left dead, or outside what the task agreed | style, whether the logic is right |

Drop a lane with nothing to judge, like correctness on a diff with no code in it.

Every lane gets the same brief around its question:

- Read the diff file, by path.
- Read every instruction that applies, global and project.
- Judge the diff against them, with the file, the line, and the evidence for each finding.
- Mark each finding blocking or not, against the test above, quoted in the brief in full.
- What the task agreed to do, and what's out of scope. Without it, each round moves the target.
- Fix nothing, stay in your lane, and run no reviewer of your own.

Don't say what you think is risky, and don't hand over the story of the work. A steer narrows what a reviewer looks at, and one holding the previous critique audits your fixes instead of the diff.

## Fix, then round two

Nothing blocking in any lane ends the review, and most changes stop here. Otherwise:

- Fix everything that blocks.
- A finding you disagree with gets a reason in your reply and is settled. It can't block a later round without new evidence.
- A finding in code your diff never touched is a bug your change runs into, fixed in its own commit, or unrelated work, which you report.

Then write the diff again and hand the whole thing to one fresh reviewer, same brief, no lane and no exclusion list. Its question is whether anything still blocks. Pass the settled disagreements and nothing else about the work so far, so no round numbers and no list of fixes.

## Round three is the last one

Same again, plus what round two found, since this round's question is narrow: whether each finding is cleared, and whether the fixes broke anything around them. Fixes are where new defects come from.

Then stop, whatever it says. Fix what blocks and put anything still open at the top of your report.

## Sweep the rest

Take one pass at the non-blocking findings from every round and fix what's cheap. Nobody reviews that pass, so run the repo's checks over it before you commit. List what you left, and why.

## When it runs

- Autonomous mode: before committing, except the trivial short path and tasks that end in findings.
- Collaborative mode: when the user picks it among the closing steps.
