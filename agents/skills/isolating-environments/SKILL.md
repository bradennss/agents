---
name: isolating-environments
description: Spin up dev servers, containers, databases, and other stateful services so two checkouts of the same project can run side by side without colliding. Use when starting a stack to exercise a change, when choosing ports, container names, database names, or temp paths, when a run fails because a port or resource is already taken, or when a shared resource can only serve one client at a time.
---

# Isolating environments

Another worktree may be running the same stack right now, so build one that can't collide with it.

## Name everything after the branch

Put the branch name into every resource you create: containers, compose projects, volumes, databases, queues, and temp paths.

## Let the tool pick the port

Bind to port 0 or let the tool choose, read back the port it picked, and use that value for the rest of the setup and for every check you run.

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
