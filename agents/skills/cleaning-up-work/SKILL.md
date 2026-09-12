---
name: cleaning-up-work
description: Put the machine back the way you found it at the end of a task. Use when a task is wrapping up, when dev servers, containers, databases, tunnels, or background processes from the work are still running, or when scratch files, test artifacts, test data, scratch branches, or lock files are left on disk.
---

# Cleaning up work

Stop what you started and delete what you made:

- Dev servers, background processes, containers, tunnels, and databases.
- Temporary files, scratch scripts, test artifacts, and test data.
- Scratch branches.

Leave the lock files from the `setting-up-work` skill on disk, since deleting one between runs lets two processes lock different files.

The worktree and its branch go through the `landing-work` skill, so leave those alone here.

When something has to keep running or stay on disk for the work to hold, say what it is and why.
