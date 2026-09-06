# Install this repo into your agent

You're an agent, and a user pasted a link to this file so you'd install it. This repo holds shared config for coding agents: a global rules file and a set of skills. Read this, set it up so you load both every time you start, then tell the user what you did.

## Get the repo onto disk

Your links point at a local copy, so it needs to live somewhere stable. Find an existing copy before cloning a new one, so a reinstall reuses the same repo instead of making a duplicate. Check in this order:

- If you're already running inside a clone of this repo (there's an `agents/AGENTS.md` next to this `INSTALL.md`), use it where it is.
- Otherwise check whether you installed this before. Look at the global rules symlink you'd create below (see "Wire it in"). If it exists and resolves to a real `agents/AGENTS.md`, the repo root is the directory two levels up from that file. Use that clone.
- Otherwise clone it somewhere durable you won't delete, such as where you keep your own projects:

  ```
  git clone https://github.com/bradennss/agents.git
  ```

Get the absolute path of the repo root. The two things you'll link are `agents/AGENTS.md`, the global rules for every project, and `agents/skills`, a directory of skills with one folder each and a `SKILL.md` inside.

## Wire it in

1. Work out where your own agent reads its global config: the path for a global context or rules file, and the directory it scans for global skills. Use what you know about yourself, or check your own docs. If you can't find one of them, say so and skip it instead of guessing.

2. Point each one at the repo with a symlink: the global rules path at `agents/AGENTS.md`, and the global skills directory at `agents/skills`. Create parent directories as needed.

3. Don't overwrite anything. If a target is already a symlink, replace it so you can reinstall safely. If it's a real file or directory you didn't create, leave it and report it so the user can merge it by hand. Otherwise create the link.

## Verify and report

- Confirm each link you made resolves to a real path under the repo's `agents/`.
- Load your global config fresh from an unrelated directory and confirm you see the global rules and the skills.
- Tell the user, in a short list, where you cloned the repo, which links you created, which you skipped and why, and the result of the check.
