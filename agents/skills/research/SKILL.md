---
name: research
description: Use when you need to understand an external library, service API, framework choice, concept, or current engineering practice before planning or building. Covers gathering from the web across varied angles, verifying claims against sources, and pulling detail from pages and repos to reach a grounded understanding with citations. Trigger whenever a task depends on facts that change over time, like package versions, API shapes, or recommended approaches, even when the user doesn't ask you to research.
---

# Research

Don't rely on training knowledge for anything that changes over time, like library docs, service APIs, recommended frameworks, version numbers, and release dates. Check it against live sources before you rely on it, and research before you plan or build.

## When to use

- You're about to pick or use an external library, framework, or service.
- You need an API shape, a config option, a version, or a release date.
- You want the current recommended way to build something.

## Gathering

1. Search with `web_search` using two to four varied angles so coverage is broad. Vary scope and wording across the queries.
2. Pull the detail you need with `fetch_content` for docs pages, GitHub repos, and PDFs. Use mode `answer` to ask a page a direct question.
3. Retrieve stored slices with `get_search_content` and `findText` so you don't page through whole documents.

## Verifying

- Check important claims with `source_check` so a decision rests on a real passage.
- Prefer primary sources: official docs, the project's own repo and release notes.
- When sources disagree, note it and go with the most authoritative and most recent.

## Converging

Write down what you found with citations: the version, the API, the chosen approach, and why. That grounded summary feeds the next step.

## Next

Feed the findings into `setting-up-a-project` for a greenfield build, or into building the change.
