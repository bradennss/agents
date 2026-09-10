# Exercising a frontend with agent-browser

Work the running app in a real browser, through the same screens and flows a user touches, and read back what rendered. That's how you find out the page loads, the data arrives, the click is wired, and the next view shows what it should.

## Load the CLI's own skill first

The `agent-browser` CLI ships a version-matched usage guide. Read it before you start so you use the current commands and the ref workflow:

```sh
agent-browser skills get core --full
```

There are specialized skills too. List them with `agent-browser skills list` and load one when the target is an Electron app, Slack, a protected Vercel deployment, or a cloud browser.

## Start the real app

Bring the frontend up its usual way and point the browser at the running URL. It needs its real backend behind it, since you're checking the whole path from click to server and back. A frontend served against mocked data only shows you the layout.

## Read, act, verify

Working a UI is a loop:

1. `open` the URL, then `read` the page to see the agent-readable text and the refs for the elements on it.
2. Act on a ref or a selector: `click`, `type`, `fill`, `select`, `press`.
3. `read` again and confirm the result: the new view, the updated text, the validation message, the redirect.

Work from the refs that `read` gives you instead of guessing selectors. Refs stay stable across the steps of a flow and save you from brittle CSS paths.

```sh
agent-browser open http://localhost:5173/login
agent-browser read
agent-browser fill @email ada@example.com
agent-browser fill @password secret
agent-browser click @submit
agent-browser read
```

## Walk the flows you changed

Walk the flows your change affected, from start to finish:

- The happy path through the feature, from entry to the final rendered result.
- Form validation: submit empty, submit bad values, and confirm the messages render.
- Navigation and state: the redirect after an action, the back path, a reload that should keep or drop state.
- Loading and error states: a slow or failing request should show the state you built, not a blank screen or a crash.
- Auth-gated views: reach a protected screen while logged out and confirm it redirects or blocks.

## Confirm what rendered

Read the page back and check the real content, not just that navigation happened. Confirm the text you expected is there, the list has the rows it should, the error banner shows when it should. For a visual change, save a screenshot with the CLI's screenshot command as proof.

## Show the proof

Paste the sequence of `agent-browser` commands and the key output from `read` so a reader can see the flow ran and the page showed the right thing. Save a screenshot for anything visual and reference it.

## If the browser won't cooperate

A flow you can't get through is often the real bug. An element with no stable ref may be rendering wrong. A click that does nothing may not be wired. Chase that down instead of working around it, then run the flow again from `open`.
