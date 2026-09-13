---
name: cleaning-up-dev-environment
description: Take down what a task spun up, so the machine and the repo look the way they did before it started. Use when a task is wrapping up, when a merged branch leaves a worktree behind, when dev servers, containers, databases, tunnels, or background processes are still running, or when scratch files, test artifacts, test data, or scratch branches are still on disk.
---

# Cleaning up a dev environment

Everything the task started or created comes down, except the change itself. Use the repo's own remote name in place of `origin`, and its own default branch in place of `<default-branch>`.

## Stop what's running

- [ ] Stop the dev servers, background processes, containers, tunnels, and databases the task started. A browser tab or a tunnel the user runs themselves stays with them.
- [ ] Leave the lock files from `setting-up-dev-environment` on disk. Delete one between runs, and two processes end up locking different files.

## Delete what was created

- [ ] Temporary files, scratch scripts, and test artifacts.
- [ ] Test data and scratch branches.

Local files copied into a worktree, like `.env`, and installed dependencies go with the worktree below.

## Remove the worktree

A merged branch has nothing left to hold:

```sh
cd <repo-root>
git worktree remove .worktrees/<branch>
git branch -d <branch>
rmdir .worktrees
```

- `git worktree remove` refuses while the worktree holds modified or untracked files, though ignored files go quietly with it. `git branch -d` refuses while the branch holds commits that never reached the default branch. Either refusal means work is still in there, so stop and ask rather than reaching for `--force` or `-D`.
- `rmdir` clears the folder only once the last worktree is gone.
- Put the main checkout back on the branch it was on when the task started.
- A branch waiting on a pull request, or left for later, keeps its worktree and its branch. Say where they are.
- The `.worktrees/` line in `.git/info/exclude` stays either way.

## Say what stayed

When something has to keep running or stay on disk for the work to hold, say what it is and why.
