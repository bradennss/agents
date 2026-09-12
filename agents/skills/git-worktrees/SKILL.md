---
name: git-worktrees
description: Check that a repo is on the right branch and current with its remote, set up a git worktree on a feature branch for a task, work inside it, and land it when the work is done. Use when starting any change to an existing repo, trivial or not, when picking an earlier branch or worktree back up, when deciding whether a change is small enough to make in place, when the user asks for a worktree or a feature branch, or when a finished feature branch needs to be merged, turned into a pull request, or left for later.
---

# Git worktrees

Non-trivial work happens in a git worktree on a feature branch. A trivial change happens in place, meaning the main checkout on the default branch with no worktree and no feature branch. Trivial means one file and no behavior change, like a typo, a formatting pass, or a version bump. Building a new project also happens in place.

## Start from a current base

Every task starts at the repo root with a look at the main checkout, whether or not it's trivial:

```sh
git fetch origin
git status -sb
git log --oneline origin/<default-branch>..<default-branch>
```

- Uncommitted changes: stop and ask the user how to handle them before you create or edit anything.
- Commits in that log haven't been pushed. Tell the user what's sitting there, and use `<default-branch>` as the start point for the worktree so the work includes them.
- With no remote, or in a repo with no commits yet, skip the fetch and the log and work from the local default branch.

A trivial change happens in place, on the default branch:

- `git status -sb` shows another branch or a detached HEAD: stop and ask which branch the work belongs on.
- It shows the default branch behind the remote: bring it level with `git merge --ff-only origin/<default-branch>`. When that won't fast-forward, the histories have diverged, so stop and ask.

## Set it up

With the base current:

```sh
grep -qxF '.worktrees/' .git/info/exclude || echo '.worktrees/' >> .git/info/exclude
git worktree add --no-track .worktrees/<branch> -b <branch> origin/<default-branch>
```

- Use the repo's own remote name in place of `origin` everywhere in this skill.
- Name the branch as a kebab-case slug of the task, like `worktree-rules` or `login-retry-fix`. Leave out prefixes, dates, and owner names.
- The worktree path matches the branch name.
- `.worktrees/` stays out of git through `.git/info/exclude`. Leave the repo's `.gitignore` alone. The exclude line stays in the repo once it's there.

## Make it runnable

Copy the ignored local files the project needs to run, like `.env`, then install dependencies. Those files carry the main checkout's ports and resource names, so replace those values following the `isolating-environments` skill before you start anything. Tell the user what you copied, what you installed, and what you changed.

## Work in it

`cd` into the worktree and do every step of the work there, including the commits. Give every subagent the worktree path as its working directory.

## Pick work back up

Going back to a branch or worktree from earlier work is starting new work, so bring the branch current before you add to it. Run this from the worktree or checkout that holds the branch, with nothing uncommitted:

```sh
git fetch origin
git rebase origin/<default-branch>
```

When the rebase hits a conflict, or the branch is pushed and someone else could be building on it, stop and ask.

## Land it

Once the work is committed, ask the user which of these they want:

- Merge locally. `cd` back to the repo root, switch to the default branch, run `git merge --no-ff <branch>`, then `git worktree remove .worktrees/<branch>` and `git branch -d <branch>`. Delete the empty `.worktrees` folder once the last worktree is gone, and put the main checkout back on the branch it started on.
- Open a pull request. Run `git push -u origin <branch>` from the worktree, then open the PR with `gh pr create`. The worktree and the branch stay in place.
- Neither for now. Leave both alone and tell the user the branch name and the worktree path.
