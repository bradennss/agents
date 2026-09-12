---
name: designing-frontends
description: "Design a screen or a flow in rounds: rough takes on direction first, then layout, behavior, and polish passes with screenshots. Use when the open question is how something looks or feels, when building a screen or flow without a settled design, when styling, layout, spacing, or motion is up for decision, or when the user asks for design options or a redesign."
---

# Designing frontends

Design work happens in short passes over the whole surface, rather than one stretch that ends with a finished screen. Building a screen from a design the user already settled is ordinary work, so the ordinary rules cover that.

## Agree the direction first

Put up two or three rough takes on layout, type, color, and spacing. Say which one you'd pick and why.

- Collaborative mode: wait for the answer before building anything.
- Autonomous mode: the takes go in the question pass before the first edit, with the one you'd pick as the default.

## Then work the surface in passes

Every pass covers every screen in the flow before the next one starts.

1. Static layout, with real copy and real data.
2. Behavior: interaction, navigation, and the loading, empty, and error states.
3. Polish: spacing, type scale, color, motion, and breakpoints.

Don't finish one screen to the end while the others sit at pass one. A flow that's consistent at pass one is worth more than one screen that's done.

## Show the work

Keep a dev server running, started through the `setting-up-dev-environment` skill.

Open the app in the user's own browser as soon as the server is up, so they watch the screens change while you work:

```sh
open http://localhost:<port>        # macOS
xdg-open http://localhost:<port>    # Linux
```

Use the port the server actually picked, and say the URL in chat so the user can get back to it. Open it once per session, since hot reload carries the rest. When the project has no hot reload, say which pages the user has to refresh.

Screenshot the affected screens with the `agent-browser` CLI after each pass, at the breakpoints that matter. Those go in the check-in, since the user reads it later and can't scroll back through a live page.

- Collaborative mode: each pass ends at a check-in. Show the screenshots, say what you'd change next, name what you're unsure about, then stop. The next pass starts when the user says it does. Leave the server running between passes so the browser stays live.
- Autonomous mode: nobody's watching, so skip the browser and rely on screenshots. Run the passes without stopping, and put the screenshots from each pass in the final report.

## Keep the passes cheap

No reviewer subagent and no commits between passes. Run the closing steps once, when the design is done.
