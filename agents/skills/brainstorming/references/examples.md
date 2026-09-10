# Worked examples

Two full brainstorming loops, from a vague prompt to a confirmed scope. Use them as a model for the shape of the loop. Don't reuse the specifics.

## Example 1: "Build me something to organize my downloads folder"

**Restate.** "You want a tool that sorts the files in your Downloads folder into folders on its own. Right?" User confirms, and says it's a mess of screenshots, PDFs, and installers.

**Questions (batched).**

- Do you want to run it yourself, or should it run in the background?
- How should it group things, by type or by date?
- What about files it doesn't recognize?
- Just your Mac, or does it need to work for other people?

**Answers.** They'll run it themselves when the folder gets bad, want it grouped by type, and anything it doesn't recognize should stay put. It's only for their Mac.

**Default and confirm.** "I'd make it a small command-line script you run when you want. It moves files into folders like Images, Documents, and Installers by extension, and leaves anything it doesn't know alone. Python, since it's already on your Mac. Sound right?" User agrees, and asks that it never overwrite a file that's already at the destination.

**Anti-scope.** Out of scope: no background watching, no dedup, no cloud.

**Confirmed scope.**

- Goal: tidy the Downloads folder on demand.
- User: the owner, on one Mac.
- Must: group files into type folders by extension, skip unknown types, never overwrite a file that's already at the target.
- Constraints: Python, local, standard library only.
- Out of scope: background daemon, dedup, remote storage.
- Works when: running it on a messy folder sorts the known types and leaves the rest, with no file lost or overwritten.

## Example 2: "I want a dashboard for our team's metrics"

**Restate.** "You want a dashboard that shows your team's key metrics in one place. Which metrics, and where do they come from?" User: deploy frequency, open pull requests, on-call pages.

**Questions (batched).**

- Where does each of these live right now?
- Who looks at it, and how often?
- Does it need to update live, or is once a day enough?
- Does it need a login, or is it open inside the network?

**Answers.** The numbers come from GitHub and PagerDuty. The whole team looks at it during standup. Once a day is plenty. It sits behind the VPN, so there's no login.

**Options with tradeoffs.** "Two ways to go. One, a hosted tool like Grafana with data source plugins. It's quick to stand up, but it's another service to run. Two, a small static page that a scheduled job rebuilds from the APIs once a day. It's easy to host, but you write the fetch code. For a daily refresh of three metrics, I'd pick the static page." User picks the static page.

**Pre-mortem.** "Imagine this shipped and went wrong. What broke?" User: stale data shown as if it were fresh. So stamp the page with the last refresh time, and show an error if a fetch failed rather than the old numbers.

**Confirmed scope.**

- Goal: one page of three team metrics you can glance at.
- Users: the team, at standup.
- Must: deploy frequency and open PRs from GitHub, on-call pages from PagerDuty; rebuilt daily; shows the last refresh time; shows a clear error on a failed fetch.
- Constraints: internal behind the VPN, no login, served as a static page.
- Out of scope: live updates, per-user views, history beyond what fits on one page.
- Works when: the scheduled job pulls all three metrics and publishes a page with the right timestamp, and a forced fetch failure shows an error instead of old numbers.

## What both loops do

- Start with the restate. It catches detail the prompt left out.
- Batch the questions, and only ask ones that would change what you build.
- Use a default or a set of tradeoffs to move the decision along instead of leaving it open.
- End with a summary that names the goal, the users, the must-haves, the constraints, what's out of scope, and a concrete test, so the next step can start without asking again.
