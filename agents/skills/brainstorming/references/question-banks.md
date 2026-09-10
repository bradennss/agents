# Question banks

Questions to draw from when pinning down a vague request. Don't ask all of them. Pick the few whose answers will actually change what you build, and batch them so the user answers in one pass. Ask them in your own words, not word for word off this list.

## By dimension

These four dimensions cover most of what a brief needs. Work through them and stop once nothing important is open.

### Goal and users

- What's the problem, and what do you do about it now?
- Who uses it?
- How often do they reach for it?
- Are they comfortable with this kind of tool?
- Is it for you, for a team, or for people you don't control?
- What does a good result look like to them?

### Scope

- What does it absolutely have to do?
- What's nice to have but not essential?
- What should it not do?
- Is this a throwaway, or will you keep working on it?
- What's the smallest version you'd still use?

### Constraints

- Is the language or framework already decided?
- Where does it run?
- Does it have to fit with anything you already have?
- Is there a deadline or a budget?
- Any rules it has to follow, like security or privacy?

### Success

- How will you know it's working?
- What's the first thing you'll try it on?
- Is there something that can't break?
- Who decides it's done?

## By project type

Start from the dimension questions, then add the ones that fit the kind of thing being built.

### CLI tool or script

- What goes in? Flags, a file, stdin?
- What does it print, and does anything read that output?
- Quiet when it works, or does it talk?
- Does it run once and stop, or keep going?
- What should happen when something goes wrong?

### Web app or service

- What are the main pages or endpoints?
- Who's allowed to do what?
- Does the data need to stick around, and where?
- How many people hit it at once?
- What other services does it lean on, and what if they're down?

### Library or package

- Who imports it, and into what?
- What's public, and what stays private?
- What runtimes or versions does it have to support?
- How do people install it?
- What happens to users when you change it?

### Data pipeline or batch job

- Where does the data come from, and where does it end up?
- What does the input look like, and how clean is it?
- Does it run on a schedule, or all the time?
- What do you do with a bad record?
- Is it safe to run the same job twice?

### Change to existing code

- What's actually wrong right now?
- What has to keep working the same?
- Does anything depend on the current behavior?
- Is this a fix, a cleanup, or a new feature?
- How will you check you didn't break anything?
