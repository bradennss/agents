---
name: setting-up-dev-environment
description: "Get a place to work before touching code: the right branch, current with its remote, in a git worktree when the change needs one, with the local files, dependencies, ports, and names that let the stack run beside other checkouts. Use when starting any change to an existing repo, trivial or not, when picking an earlier branch or worktree back up, when deciding whether a change is small enough to make in place, when a checkout needs the files and dependencies that make it runnable, when choosing ports, container names, database names, or temp paths, when a run fails because a port or another resource is already taken, or when a shared resource can only serve one client at a time."
---

# Setting up a dev environment

Do this before the first edit. You end up on the branch you meant to be on, built from current code, able to run without colliding with anything else on the machine.

- Non-trivial work gets a git worktree on a feature branch.
- Trivial work, meaning one file and no behavior change, happens in place on the default branch, or in the session's worktree when there is one. A new project is built in place too.
- Swap in the repo's own remote name wherever this says `origin`, and its own default branch wherever it says `<default-branch>`.

## Check the base

Every change starts here, at the repo root:

```sh
git fetch origin
git status -sb
git log --oneline origin/<default-branch>..<default-branch>
```

- Uncommitted changes in the main checkout that you didn't make: stop and ask before you create or edit anything.
- Commits in that log, meaning unpushed work on the local default branch: say what's sitting there, then start the branch from `<default-branch>` rather than `origin/<default-branch>`.
- No remote, or no commits yet: skip the fetch and the log, and work from the local default branch.

Work in place needs the main checkout itself to be right:

- Another branch or a detached HEAD: stop and ask which branch the work belongs on.
- The default branch behind the remote: `git merge --ff-only origin/<default-branch>`. A refusal means the histories diverged, so stop and ask.

Then run the repo's own checks against the base, so you know whether the tests and the build were already failing. Run them where the dependencies are: the main checkout, or the worktree once it's installed. A red base is a blocker to report rather than build on.

Note the starting commit with `git rev-parse HEAD` and say it in your reply, since the review diff is taken against it. A repo with no commits has none, so say that instead.

## Make the worktree

```sh
grep -qxF '.worktrees/' .git/info/exclude || echo '.worktrees/' >> .git/info/exclude
git worktree add --no-track .worktrees/<branch> -b <branch> origin/<default-branch>
```

- Name the branch as a kebab-case slug of the task, like `login-retry-fix`. No prefixes, dates, or owner names.
- The worktree path matches the branch name.
- `.worktrees/` goes in `.git/info/exclude`, not the repo's `.gitignore`.
- Note the starting commit with `git rev-parse HEAD` and say it in your reply.

Then `cd` into the worktree. Everything after this happens there, commits included, and every subagent gets that path as its working directory.

## Make the checkout runnable

- Copy the ignored local files the project needs, such as `.env`.
- Install dependencies with the project's package manager.
- Change the ports and resource names those copied files carry over from the main checkout.
- Say what you copied, what you installed, and what you changed.

## Keep the stack to itself

Another checkout might be running this stack right now.

- Put the branch name into every resource the run creates: containers, compose projects, volumes, databases, queues, temp paths.
- For ports, bind to port 0 or let the tool choose, read back the port it picked, and use that value everywhere after. Inside a container, pick from the ports it publishes, since a random one isn't reachable from outside.

## Lock what can't be isolated

Some resources serve one client at a time, like a shared remote database, a port a vendor callback has to reach, or a physical device. Run against those one at a time under a machine wide lock named after the resource, held until the run finishes.

```sh
lockf -k /tmp/<resource>.lock <command>   # macOS
flock /tmp/<resource>.lock <command>      # Linux
```

Both wait for the lock by default. Leave the lock file on disk, since deleting it between runs lets two processes lock different files. On macOS `-k` is what keeps the file, and `lockf` guarantees lock ordering only with it.

## Pick an earlier branch back up

Picking a branch back up is starting a task, so it takes a mode, a session name, and the base check above. Run this in the worktree or checkout holding the branch, with nothing uncommitted:

```sh
git fetch origin
git rebase origin/<default-branch>
```

- Stop and ask when the rebase conflicts, or when the branch is pushed and someone else could be building on it.
- The rebase makes the old starting commit stale. Note the new one with `git rev-parse origin/<default-branch>` and say it in your reply.
- Check the environment is still there: dependencies installed, local files present, nothing else holding the ports.
