---
name: setting-up-dev-environment
description: Get a place to work before touching code: the right branch, current with its remote, in a git worktree when the change needs one, with the local files, dependencies, ports, and names that let the stack run beside other checkouts. Use when starting any change to an existing repo, trivial or not, when picking an earlier branch or worktree back up, when deciding whether a change is small enough to make in place, when a checkout needs the files and dependencies that make it runnable, when choosing ports, container names, database names, or temp paths, when a run fails because a port or another resource is already taken, or when a shared resource can only serve one client at a time.
---

# Setting up a dev environment

Do this before the first edit. You end up with a checkout on the branch you meant to be on, built from current code, able to run without colliding with anything else on the machine.

Non-trivial work gets a git worktree on a feature branch. Trivial work happens in place, in the main checkout on the default branch. Trivial means one file and no behavior change, like a typo, a formatting pass, or a version bump. A new project is built in place too.

Swap in the repo's own remote name wherever this says `origin`, and its own default branch name wherever it says `<default-branch>`.

## Check the base

Every change starts here, at the repo root:

```sh
git fetch origin
git status -sb
git log --oneline origin/<default-branch>..<default-branch>
```

`git status -sb` names the checked out branch and how far it sits from its own upstream. The log lists commits on the local default branch that were never pushed, and it does that whichever branch is checked out.

- Uncommitted changes in the main checkout: stop and ask what to do with them before you create or edit anything.
- Commits in that log: say what's sitting there, then start the branch from `<default-branch>` rather than `origin/<default-branch>`, so the work includes them.
- No remote, or no commits yet: skip the fetch and the log, and work from the local default branch.

Work in place also needs the main checkout itself to be right:

- Another branch or a detached HEAD: stop and ask which branch the work belongs on.
- The default branch behind the remote: `git merge --ff-only origin/<default-branch>`. If that refuses, the histories diverged, so stop and ask.

## Make the worktree

```sh
grep -qxF '.worktrees/' .git/info/exclude || echo '.worktrees/' >> .git/info/exclude
git worktree add --no-track .worktrees/<branch> -b <branch> origin/<default-branch>
```

- Name the branch as a kebab-case slug of the task, like `login-retry-fix`. No prefixes, no dates, no owner names.
- The worktree path matches the branch name.
- `.worktrees/` stays out of git through `.git/info/exclude`, not the repo's `.gitignore`. Once the line is there, it stays in the repo.

Then `cd` into the worktree. Everything after this happens there, commits included, and every subagent gets that path as its working directory.

## Make the checkout runnable

A fresh worktree holds only what git tracks. Copy over the ignored local files the project needs, such as `.env`, then install dependencies with the project's package manager.

Those copied files carry the ports and resource names of the main checkout, so change them before you start anything. Say what you copied, what you installed, and which values you changed.

## Keep the stack to itself

Another checkout might be running this stack right now.

Put the branch name into every resource the run creates: containers, compose projects, volumes, databases, queues, and temp paths.

For ports, bind to port 0 or let the tool choose, read back the port it picked, and use that value through the rest of the setup and in every check you run.

## Lock what can't be isolated

Some resources serve one client at a time, like a shared remote database, a port that a vendor callback has to reach, or a physical device. Run against those one at a time instead of working around them.

Take a machine wide lock named after the resource and hold it until the run finishes.

On macOS:

```sh
lockf -k /tmp/<resource>.lock <command>
```

On Linux:

```sh
flock /tmp/<resource>.lock <command>
```

Both wait for the lock by default. Leave the lock file on disk, since deleting it between runs lets two processes lock different files. On macOS `-k` is what keeps the file, and `lockf` guarantees lock ordering only with it.

## Pick an earlier branch back up

Returning to a branch or a worktree from earlier work needs the same current base. Run this in the worktree or checkout that holds the branch, with nothing uncommitted:

```sh
git fetch origin
git rebase origin/<default-branch>
```

Stop and ask when the rebase hits a conflict, or when the branch is pushed and someone else could be building on it.

Then check the environment is still there: dependencies installed, local files present, nothing else holding the ports.
