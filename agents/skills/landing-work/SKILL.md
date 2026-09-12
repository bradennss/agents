---
name: landing-work
description: Land a committed branch by merging it, pushing it, opening a pull request, or leaving it for later, and remove the worktree that held it. Use when the work is committed and the user needs to decide where the branch goes, when a feature branch is ready to merge or review, or when a worktree and its branch are done with.
---

# Landing work

The work is committed. Ask the user which of these they want:

- Merge locally. `cd` back to the repo root, switch to the default branch, run `git merge --no-ff <branch>`, then `git worktree remove .worktrees/<branch>` and `git branch -d <branch>`. Delete the empty `.worktrees` folder once the last worktree is gone, and put the main checkout back on the branch it started on.
- Open a pull request. Run `git push -u origin <branch>` from the worktree, then open the PR with `gh pr create`. The worktree and the branch stay in place.
- Neither for now. Leave both alone and tell the user the branch name and the worktree path.

Use the repo's own remote name in place of `origin`. The `.worktrees/` line in `.git/info/exclude` stays in the repo either way.

Work done in place has no branch to land, so it stops at the commit.

Once the branch is handled, follow the `cleaning-up-work` skill.
