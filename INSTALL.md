# Install this repo into your agent

You are an agent, and a user pasted a link to this file so you'd install it. This repo holds shared config for coding agents: a global rules file and a set of skills. Read this, set it up so you load both on every start, then report what you did.

## Get the repo onto disk

Your links will point at a local copy, so it has to live somewhere stable.

- If you're already running inside a clone of this repo (there's an `agents/AGENTS.md` next to this `INSTALL.md`), use it where it is.
- Otherwise clone it to a durable location you won't delete, like where you keep your own projects:

  ```
  git clone https://github.com/bradennss/agents.git
  ```

Get the absolute path of the repo root. The two things you'll link are `agents/AGENTS.md` (global rules meant for every project) and `agents/skills` (a directory of skills, one folder each with a `SKILL.md`).

## Wire it in

1. Work out where your own agent reads its global config: the path for a global context or rules file, and the directory it scans for global skills. Use what you know about yourself, or check your own docs. If you can't find one of them, say so and skip it rather than guessing.

2. Point each at the repo with a symlink: the global rules path at `agents/AGENTS.md`, the global skills directory at `agents/skills`. Create parent directories as needed.

3. Don't clobber anything. If a target is already a symlink, replace it so reinstalling is safe. If it's a real file or directory you didn't create, leave it and report it so the user can merge it by hand. Otherwise create the link.

## Verify and report

- Confirm each link you made resolves to a real path under the repo's `agents/`.
- Load your global config fresh from an unrelated directory and confirm you actually see the global rules and the skills.
- Tell the user, in a short list, where you cloned the repo, which links you created, which you skipped and why, and the result of the check.
