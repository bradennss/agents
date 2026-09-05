---
name: writing-commits
description: >
  Write commits, PRs, and review comments the way this repo wants them: matched to the existing git history, with clean subject lines and no AI credit. Use when committing, writing a commit message, opening a PR, or writing a PR title, PR body, or review comment, even if the user just says "commit this" or "open a PR."
---

# Writing commits

Match the style already in the repo, then follow the rules below. Run `git log` first and read a handful of recent commits so your subject and body fit what's there.

## Rules

- No self-credit anywhere in commits, PR titles, PR bodies, review comments, or code. Skip the "Generated with" line, the AI `Co-authored-by`, the bot signature, and the emoji tag.
- Subject line: write it as an order, under 72 characters, that says what changed. Like "Add retry to the upload path."
- Body: add one only when a reviewer needs context the diff can't show. Skip it for small changes, keep it to a line or two, and never narrate the edit.
- One change per commit.
- Never force push a shared branch, commit secrets, or add files the repo ignores.
