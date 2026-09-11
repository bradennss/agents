---
name: git-worktrees
description: Set up a git worktree on a feature branch for a task, work inside it, and land it when the work is done. Use when starting any non-trivial change to an existing repo, when deciding whether a change is small enough to make in place, when the user asks for a worktree or a feature branch, or when a finished feature branch needs to be merged, turned into a pull request, or left for later.
---

# Git worktrees

Non-trivial work happens in a git worktree on a feature branch. A trivial change happens in place, meaning the main checkout on its current branch with no worktree and no feature branch. Trivial means one file and no behavior change, like a typo, a formatting pass, or a version bump. Building a new project also happens in place.

## Set it up

Start at the repo root and look at the main checkout. If it has uncommitted changes, stop and ask the user how to handle them before you create anything.

Once it's clean:

```sh
git fetch origin
grep -qxF '.worktrees/' .git/info/exclude || echo '.worktrees/' >> .git/info/exclude
git worktree add --no-track .worktrees/<branch> -b <branch> origin/<default-branch>
```

- Use the repo's own remote name in place of `origin` everywhere in this skill. With no remote, skip the fetch and branch off the local default branch.
- Name the branch as a kebab-case slug of the task, like `worktree-rules` or `login-retry-fix`. Leave out prefixes, dates, and owner names.
- The worktree path matches the branch name.
- `.worktrees/` stays out of git through `.git/info/exclude`. Leave the repo's `.gitignore` alone. The exclude line stays in the repo once it's there.

## Make it runnable

Copy the ignored local files the project needs to run, like `.env`, then install dependencies. Those files carry the main checkout's ports and resource names, so replace those values following the `isolating-environments` skill before you start anything. Tell the user what you copied, what you installed, and what you changed.

## Work in it

`cd` into the worktree and do every step of the work there, including the commits. Give every subagent the worktree path as its working directory.

## Land it

Once the work is committed, ask the user which of these they want:

- **Merge locally.** `cd` back to the repo root, switch to the default branch, run `git merge --no-ff <branch>`, then `git worktree remove .worktrees/<branch>` and `git branch -d <branch>`. Delete the empty `.worktrees` folder once the last worktree is gone, and put the main checkout back on the branch it started on.
- **Open a pull request.** Run `git push -u origin <branch>` from the worktree, then open the PR with `gh pr create`. The worktree and the branch stay in place.
- **Neither for now.** Leave both alone and tell the user the branch name and the worktree path.
