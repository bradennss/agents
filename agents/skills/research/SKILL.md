---
name: research
description: Use when you need to understand an external library, service API, framework choice, concept, or current engineering practice before planning or building. Covers gathering from the web across varied angles, verifying claims against sources, and pulling detail from pages and repos to reach a grounded understanding with citations. Trigger whenever a task depends on facts that change over time, like package versions, API shapes, or recommended approaches, even when the user doesn't ask you to research.
---

# Research

Don't rely on training knowledge for anything that changes over time, like library docs, service APIs, recommended frameworks, version numbers, and release dates. Check it against live sources before you rely on it, and research before you plan or build. Training data has a cutoff and it's stale for exactly the facts a build depends on.

## When to use

- You're about to pick or use an external library, framework, or service.
- You need an API shape, a config option, a version, or a release date.
- You want the current recommended way to build something.
- You're repeating a fact from memory that could have changed since your training cutoff.

If the fact is stable and general, like how a sort works or what a mutex is, skip research and answer directly. Research earns its cost when the answer moves over time or when getting it wrong is expensive.

## The loop

Research runs in three passes: gather, verify, converge. Small questions need one light pass. A framework choice or an unfamiliar API needs several rounds before you trust the answer.

### Gather

1. Search the web from two to four varied angles so coverage is broad. Vary scope and wording across the searches instead of asking the same thing three ways. See `references/query-design.md` for how to pick angles.
2. Open the sources that matter and read the detail: docs pages, the project's repo, release notes, and PDFs. When a page is long, search within it for the passage you need instead of reading the whole thing.
3. Keep track of where each fact came from as you go, so you can cite it later without refetching.

### Verify

- Confirm the load-bearing claims against the actual source text, not a summary you half remember. A decision should rest on a real passage.
- Prefer primary sources: official docs, the project's own repo, and its release notes. Rank a random blog below the project's own words.
- Confirm the source is current. A version number or API shape from an old page is worse than no answer. Check the page or release date.
- When sources disagree, note it and go with the most authoritative and most recent.

Full guidance on judging sources and resolving conflicts is in `references/source-quality.md`.

### Converge

Write down what you found with citations: the version, the API, the chosen approach, and why. Link the source for each load-bearing fact so the next step, and the user, can check it. That grounded summary is the output of research and it feeds whatever comes next.

## Reference files

- `references/query-design.md`: how to turn a research need into two to four search angles that cover it, with good and bad query sets. Read it when a single search isn't giving broad coverage.
- `references/source-quality.md`: judging authority and recency, spotting stale or invented facts, and resolving disagreement between sources. Read it before a decision rests on what you found.

## Next

Feed the findings into `setting-up-a-project` for a greenfield build, into `brainstorming` when the research reshapes the scope, or straight into building the change.
