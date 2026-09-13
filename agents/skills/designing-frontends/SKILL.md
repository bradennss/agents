---
name: designing-frontends
description: "Design a screen or a flow in rounds: rough takes on direction first, then layout, behavior, and polish passes with screenshots. Use when the open question is how something looks or feels, when building a screen or flow without a settled design, when styling, layout, spacing, or motion is up for decision, or when the user asks for design options or a redesign."
---

# Designing frontends

Design work happens in short passes over the whole surface. Building from a design the user already settled is ordinary work, and so is adding a field or a loading state to a settled screen.

## Agree the direction first

Put up two or three rough takes on layout, type, color, and spacing, and say which one you'd pick. Show a take rather than describing it: build it and screenshot it when something already runs, otherwise write out the layout, the type scale, the colors, and the spacing values.

- Collaborative mode: wait for the answer before building the direction out.
- Autonomous mode: the takes go in the question pass, with the one you'd pick as the default.

## Then work the surface in passes

Every pass covers every screen in the flow before the next one starts:

1. Static layout, with real copy and real data.
2. Behavior: interaction, navigation, and the loading, empty, and error states.
3. Polish: spacing, type scale, color, motion, breakpoints.

Don't finish one screen while the others sit at pass one. A round in a design task is a pass, so split the work that way rather than by screen.

## Show the work

Start the project's own dev server and keep it running, with the ports and resource names from `setting-up-dev-environment`. Screenshot the affected screens with the `agent-browser` CLI after each pass, at the breakpoints that matter, and put them in the check-in or the report.

### Collaborative mode

Open the app in the user's own browser once the server is up, so they watch the screens change:

```sh
open http://localhost:<port>       # macOS
xdg-open http://localhost:<port>   # Linux
```

- Use the port the server actually picked, and say the URL in chat. Open it once per session, since hot reload carries the rest. Say which pages need a refresh when the project has no hot reload.
- `SSH_CONNECTION` in the environment means you're on a remote box, and a missing `DISPLAY` on Linux means there's no desktop to open at all. Hand over a URL the user can reach from their own machine instead: the port the container publishes, a tunnel, or both. Over SSH, give them `ssh -L <port>:localhost:<port> <host>` to run themselves, again whenever the server restarts on a new port.
- Each pass ends at a check-in, the four things in `working-collaboratively`, with the screenshots as the proof. The server stays up in between.
- A question you can't answer stops the pass. Screenshot what's built, name the screens still at the previous pass, and ask.

### Autonomous mode

Nobody's watching, so don't open the user's browser. Run the passes without stopping and put each pass's screenshots in the final report.

## Keep the passes cheap

No reviewers and no commits between passes. Proving, reviewing, committing, and teardown happen once, after the last pass, the way your mode runs them. The browser tab and any tunnel the user started are theirs: leave them running and name them when you say what stayed.
