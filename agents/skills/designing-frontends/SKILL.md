---
name: designing-frontends
description: "Design a screen or a flow in rounds: rough takes on direction first, then layout, behavior, and polish passes with screenshots. Use when the open question is how something looks or feels, when building a screen or flow without a settled design, when styling, layout, spacing, or motion is up for decision, or when the user asks for design options or a redesign."
---

# Designing frontends

Design work happens in short passes over the whole surface, rather than one stretch that ends with a finished screen. Building a screen from a design the user already settled is ordinary work, and so is adding a field or a loading and error state to a screen whose design is settled, so the ordinary rules cover those.

## Agree the direction first

Put up two or three rough takes on layout, type, color, and spacing. Say which one you'd pick and why.

Show a take rather than describing it. Build it and screenshot it when something already runs. When nothing does, write out the layout, the type scale, the colors, and the spacing values, so the user is choosing between things they can see.

- Collaborative mode: wait for the answer before you build the direction out.
- Autonomous mode: the takes go in the question pass before the first edit, with the one you'd pick as the default.

## Then work the surface in passes

Every pass covers every screen in the flow before the next one starts.

1. Static layout, with real copy and real data.
2. Behavior: interaction, navigation, and the loading, empty, and error states.
3. Polish: spacing, type scale, color, motion, and breakpoints.

Don't finish one screen to the end while the others sit at pass one. A round in a design task is a pass, so split the work that way rather than by screen.

## Show the work

Start the project's own dev server and keep it running, with the ports and resource names from the `setting-up-dev-environment` skill so it can't collide with another checkout.

Screenshot the affected screens with the `agent-browser` CLI after each pass, at the breakpoints that matter. Whoever reads it later can't scroll back through a live page, so the screenshots go in the check-in or the report.

### Collaborative mode

Open the app in the user's own browser as soon as the server is up, so they watch the screens change while you work.

On macOS:

```sh
open http://localhost:<port>
```

On Linux:

```sh
xdg-open http://localhost:<port>
```

Use the port the server actually picked, and say the URL in chat so the user can get back to it. Open it once per session, since hot reload carries the rest. When the project has no hot reload, say which pages the user has to refresh.

A server running somewhere the user isn't sitting opens nothing on their screen. `SSH_CONNECTION` in the environment says you're on a remote box, and a missing `DISPLAY` on Linux says there's no desktop to open at all, as in a container or a headless server. Hand over a URL the user can reach from their own machine instead, with whatever gets them there: the port the container publishes, a tunnel, or both. Over SSH, give them this to run on their own machine:

```sh
ssh -L <port>:localhost:<port> <host>
```

Hand it over again whenever the server restarts on a new port.

Each pass ends at a check-in, the four things in the `working-collaboratively` skill, with the screenshots as the proof. The next pass starts when the user says it does, and the server stays up in between so the browser stays live.

A question you can't answer stops the pass where it is. Screenshot what's built, name the screens still at the previous pass, and ask. A half-finished pass is fine to show, as long as you say that's what it is.

### Autonomous mode

Nobody's watching, so don't open the user's browser. Run the passes without stopping, and put the screenshots from each pass in the final report.

## Keep the passes cheap

No reviewer subagent and no commits between passes. Proving it works end to end, reviewing the diff, committing, and taking the environment down happen once, after the last pass, the way your mode runs them.

The browser tab on the user's machine and any tunnel they started are theirs. Leave them running and name them when you say what stayed.
