---
name: setting-up-work
description: Get a repo ready for a task, on the right branch and current with its remote, in a git worktree when the work calls for one, running a stack that can't collide with another checkout of the same project. Use when starting any change to an existing repo, trivial or not, when picking an earlier branch or worktree back up, when deciding whether a change is small enough to make in place, when a checkout needs the local files and dependencies that make it runnable, when choosing ports, container names, database names, or temp paths, when a run fails because a port or resource is already taken, or when a shared resource can only serve one client at a time.
---

# Setting up work

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

## Make the worktree

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

Copy the ignored local files the project needs to run, like `.env`, then install dependencies. Those files carry the main checkout's ports and resource names, so replace those values before you start anything. Tell the user what you copied, what you installed, and what you changed.

## Keep the stack off other checkouts

Another worktree may be running the same stack right now, so build one that can't collide with it.

Put the branch name into every resource you create: containers, compose projects, volumes, databases, queues, and temp paths.

For ports, bind to port 0 or let the tool choose, read back the port it picked, and use that value for the rest of the setup and for every check you run.

## Take a lock on what can't be isolated

Some resources serve one client at a time: a shared remote database, a port a vendor callback has to reach, a physical device. Run against those one at a time instead of working around them.

Take a machine wide lock named after the resource and hold it until the run finishes.

On macOS:

```sh
lockf -k /tmp/<resource>.lock <command>
```

On Linux:

```sh
flock /tmp/<resource>.lock <command>
```

Both wait for the lock by default. Leave the lock file on disk, since deleting it between runs lets two processes lock different files. On macOS that's what `-k` does, and `lockf` guarantees lock ordering only with it.

## Work in it

`cd` into the worktree and do every step of the work there, including the commits. Give every subagent the worktree path as its working directory.

Once the work is committed, follow the `landing-work` skill.

## Pick work back up

Going back to a branch or worktree from earlier work is starting new work, so bring the branch current before you add to it. Run this from the worktree or checkout that holds the branch, with nothing uncommitted:

```sh
git fetch origin
git rebase origin/<default-branch>
```

When the rebase hits a conflict, or the branch is pushed and someone else could be building on it, stop and ask.
