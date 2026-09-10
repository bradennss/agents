# Elicitation techniques

Plain questions handle most brainstorming. Use a named technique when the user is stuck, gives thin answers, keeps changing their mind, or the scope won't settle. Use one at a time and stop once the ambiguity is gone.

## Restate and reflect

Say back what you heard in one or two plain sentences and ask if it's right. Do this first, every time. It catches a wrong reading early, and it often gets the user to add detail they left out.

## Example-driven elicitation

When the answers stay vague, ask for a concrete case. Say "walk me through one real time you'd use this, start to finish." One worked example tells you the input, the output, and the normal path. Ask for a second only if the first leaves something unclear.

## Constraint mapping

List the hard limits before the features: language, platform, existing systems, deadline, budget, rules to follow. Constraints cut down the options, and a design that ignores them won't survive. Ask "what's fixed that I don't get to choose?" early on.

## Must and nice sorting

When the user lists a lot of wants, sort them out loud into must-have and nice-to-have, then confirm the split. Push for the smallest set that's still useful. This keeps the first version small and makes the cut clear, so anything added later is an addition on purpose.

## Anti-scope

Ask what's out of scope. Say "what should this not do?" A stated boundary keeps the scope from growing later, and it often turns up a hidden assumption, like the user expecting it to handle a case you were about to build for.

## Default and confirm

When a choice has an obvious best answer for the user's case, suggest it as a default with a one-line reason instead of asking an open question. Say something like "I'd store this in SQLite since it's a single local tool, sound right?" The user can still redirect you.

## Options with tradeoffs

When two or three approaches all work, lay them out with what each one gives up, then recommend one. Give real options, not a list of equals. The user decides with the tradeoffs in front of them.

## Pre-mortem

For anything with real stakes, ask the user to imagine it shipped and went wrong, then say what broke. The answers show you the cases that must not break and the risks worth designing around. Those feed into the success criteria.

## Five whys

When the request is really a proposed solution, ask why a few times until you reach the actual need. A user who asks for a config file might just want the tool to behave differently on different machines, which has an easier answer. Stop once you hit the real goal.

## Ranges and rounding

When the user can't give an exact number, offer a range. Ask "dozens, hundreds, or millions of records?" A rough size is often all the design needs, and it's easier to answer.
