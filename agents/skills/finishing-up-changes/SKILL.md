---
name: finishing-up-changes
description: "Turn finished work into commits, then decide where the branch goes: merge it into the default branch, push it, open a pull request, or leave it for later. Use when a change is done and needs committing, when a feature branch is ready to merge or to review, when the user asks to land, ship, push, or open a PR for the work, or when a branch is handed back unfinished."
---

# Finishing up changes

The code is written and proven. What's left is recording it and deciding where it goes.

Use the repo's own remote name in place of `origin`, and its own default branch name in place of `<default-branch>`.

## Commit

Commit from the worktree, on the feature branch, before anything below.

Split the work so each commit holds one thing. Fixes you made on the way, like an unrelated bug or a broken test, go in commits of their own.

Leave nothing uncommitted that belongs to the change:

```sh
git status -sb
```

Untracked files that belong to the change get added. Files that belong to the environment, like `.env` or local caches, don't.

## Decide where the branch goes

Ask the user which of these they want, and do nothing until they pick.

Merge it into the default branch.

```sh
cd <repo-root>
git switch <default-branch>
git merge --ff-only origin/<default-branch>
git merge --no-ff <branch>
```

The fast-forward comes first so you pick up anything that landed on the remote while the work happened. If it refuses, the histories diverged, so stop and ask. If the second merge conflicts, resolve it on the feature branch by rebasing there, following the `setting-up-dev-environment` skill, then merge again.

Open a pull request.

```sh
git push -u origin <branch>
gh pr create
```

Run both from the worktree. Say why the change happened in the description, since the diff shows what changed.

Leave it for later. Nothing to run. Tell the user the branch name and the worktree path so they can find it again.

Work done in place has no branch to land, so it ends at the commit.

## Then tear the environment down

Once the branch is handled, follow the `cleaning-up-dev-environment` skill. Which parts come down depends on the choice made here, so pass that choice along.
