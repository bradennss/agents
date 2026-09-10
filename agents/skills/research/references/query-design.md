# Query design

A web search gives you one answer per query, so each query is a separate lens on the topic. Two to four queries that come at the need from different angles cover far more than one query run three times with the words shuffled.

## Start from the need, not the phrasing

Write down what you actually need to know, then split it into the distinct questions inside it. A single request like "should I use Drizzle or Prisma" hides several questions: performance, type safety, migration story, ecosystem size, and how each handles the database you're on. Each of those is its own angle.

## Vary scope and wording

Good query sets move across the space. Bad ones sit in one spot and reword.

Good, because each query lands somewhere different:

```
Drizzle ORM vs Prisma performance benchmarks 2025
Drizzle vs Prisma type safety and migrations
Prisma ecosystem and adoption vs Drizzle
```

Bad, because they all return the same page:

```
Drizzle vs Prisma
Drizzle vs Prisma comparison
Drizzle vs Prisma review
```

## Angles worth covering

Pick the ones that fit the task:

- The direct question, phrased the way the docs would phrase it.
- The comparison, when you're choosing between options.
- The failure mode: "common problems with X", "X gotchas", "X migration issues".
- The recency angle: add the year or filter by recency when the answer moves fast.
- The primary source angle: add the library name plus "docs" or "changelog" or "release notes".

## Tighten with filters

- Filter by recency for anything time-sensitive, like the current recommended approach or a recent release.
- Restrict to a project's own docs domain when you want the primary source, or exclude a noisy domain when it keeps crowding out better results.
- Raise the result count only when the topic is broad and you're still missing coverage. More results is more to read, so don't reach for it by default.

## Stop when coverage is flat

You have enough when new queries return sources you've already seen and no new claim shows up. If two rounds add nothing, move to verifying and converging.
