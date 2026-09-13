---
name: finishing-up-changes
description: "Turn finished work into commits, then decide where the branch goes: merge it into the default branch, push it, open a pull request, or leave it for later. Use when a change is done and needs committing, when a feature branch is ready to merge or to review, when the user asks to land, ship, push, or open a PR for the work, or when a branch is handed back unfinished."
---

# Finishing up changes

The code is written and proven. What's left is recording it and deciding where it goes. Use the repo's own remote name in place of `origin`, and its own default branch in place of `<default-branch>`.

## Commit

Commit from the worktree on the feature branch, or from the main checkout for work done in place, before anything below.

- Split the work so each commit holds one thing. Fixes made on the way, like an unrelated bug or a broken test, get their own commits.
- Leave nothing uncommitted that belongs to the change: `git status -sb`.
- Add untracked files that belong to the change. Files that belong to the environment, like `.env` or local caches, stay out.

## Decide where the branch goes

The user's call in both modes, since pushing and merging can't be taken back. Collaborative mode asks here, autonomous mode asks in its question pass and follows the answer. No answer either way means leave it for later and say where the branch is. Work with no branch to send, like the trivial short path, skips this.

Merge it into the default branch:

```sh
cd <repo-root>
git switch <default-branch>
git merge --ff-only origin/<default-branch>
git merge --no-ff <branch>
```

The fast-forward comes first so you pick up whatever landed on the remote meanwhile. A refusal means the histories diverged, so stop and ask. A conflict in the second merge gets resolved on the feature branch by rebasing there, following `setting-up-dev-environment`, then merge again.

Open a pull request, both commands from the worktree:

```sh
git push -u origin <branch>
gh pr create
```

Say why the change happened in the description, since the diff shows what.

Leave it for later: nothing to run. Tell the user the branch name and the worktree path.

Work done in place has no branch to land, so it ends at the commit, sitting unpushed on the default branch. Say that's where it is.

## Then tear the environment down

Follow `cleaning-up-dev-environment` once the branch is handled, unless the user asked to keep the environment up or the work never started anything. Pass along the choice made here, since it decides which parts come down.
