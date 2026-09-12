---
name: finishing-up-work
description: Land a finished branch and leave the machine clean. Use when the work is committed and needs to be merged, pushed, turned into a pull request, or left for later, when a worktree and its branch are done with, or when a task ends and the servers, containers, scratch files, and locks it started are still around.
---

# Finishing up work

The work is committed. What's left is landing the branch and putting the machine back the way you found it.

## Land the branch

Ask the user which of these they want:

- Merge locally. `cd` back to the repo root, switch to the default branch, run `git merge --no-ff <branch>`, then `git worktree remove .worktrees/<branch>` and `git branch -d <branch>`. Delete the empty `.worktrees` folder once the last worktree is gone, and put the main checkout back on the branch it started on.
- Open a pull request. Run `git push -u origin <branch>` from the worktree, then open the PR with `gh pr create`. The worktree and the branch stay in place.
- Neither for now. Leave both alone and tell the user the branch name and the worktree path.

Use the repo's own remote name in place of `origin`. The `.worktrees/` line in `.git/info/exclude` stays in the repo either way.

Work done in place has no branch to land, so it stops at the commit.

## Put the machine back

Stop what you started and delete what you made:

- Dev servers, background processes, containers, tunnels, and databases.
- Temporary files, scratch scripts, test artifacts, and test data.
- Scratch branches.

Leave the lock files from the `isolating-environments` skill on disk, since deleting one between runs lets two processes lock different files.

When something has to keep running or stay on disk for the work to hold, say what it is and why.
