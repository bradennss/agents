# Build phase: vertical slices

Grow the product as thin end-to-end slices, never as separate horizontal layers. A slice delivers a usable capability across every layer it touches. A layer on its own delivers nothing you can run.

## Walking skeleton first

The first build task is a walking skeleton: the smallest real flow that runs end to end through every part the product needs. UI or entry point, business logic, data store, security, and CI, all connected.

- It is production code you keep and extend, not a prototype you throw away.
- It proves the architecture and the integration points before you invest in features.
- It gives every later slice a spine to attach to.

Example skeleton for a web app: a single page posts one field to a real endpoint, the endpoint writes to the real database, reads it back, and one end-to-end test runs in CI. Nothing more.

## Slice by user outcome

Decompose each epic by what the user can now do, not by technical layer.

- Good slice: "user saves and reloads a display name". It cuts through UI, API, and storage.
- Bad slice: "build the settings database schema", then "build the settings API", then "build the settings UI". Nothing is usable until all three land.

After each task in a slice, a real user can do a little more than before. If a task moves no user-facing capability forward, question whether it belongs now.

Stubs are allowed for non-critical branches inside a slice, so the end-to-end path stays thin. The critical path must be real.

## Wire the dependencies

Encode the order in the graph so `bd ready` only ever shows work you can actually start.

- A slice that consumes another slice's output is blocked by it: `bd dep add <consumer> <producer>`.
- Slices that do not touch each other stay unblocked, so they surface together and can run in parallel.
- Serialize anything that shares a resource, changes architecture, or is irreversible. Do not parallelize those even if the graph would allow it.
- Add an explicit integration task after parallel slices merge. Tasks that look independent often share a hidden interface or naming choice.

```bash
bd create "Checkout epic" -t epic                              # -> bd-c1
bd create "Cart totals slice" -t task --parent bd-c1           # -> bd-c1.1
bd create "Payment slice" -t task --parent bd-c1               # -> bd-c1.2
bd create "Order confirmation slice" -t task --parent bd-c1 \
  --deps depends-on:bd-c1.1,depends-on:bd-c1.2                  # needs both above
bd create "Checkout integration and e2e" -t task --parent bd-c1 \
  --deps depends-on:bd-c1.3                                     # after the merge
```

## Plan detail by distance

Fully specify only the next slice. Keep later slices coarse and re-plan after each release teaches you something.

- Next slice: scope, non-goals, acceptance criteria, dependencies, and how it gets verified.
- Later slices: a title and a rough appetite. Do not over-wire a future you have not learned yet.

Pull risky slices forward. If a slice validates an uncertain integration, performance assumption, or data migration, do it early while the cost of being wrong is low.

## Milestone exit criteria

A slice or milestone is done when all of these hold, not when a component reads "90% done":

- It works end to end in a production-like environment.
- Automated verification covers the path, and you have run it.
- It meets the acceptance criteria from the spec.
- It is demonstrable. You could show someone the capability working.

Close the task with the evidence in the message:

```bash
bd close bd-c1.1 -r "Cart totals correct incl tax and discount. e2e test green in CI, verified manually with 3 carts."
```

## When scope grows mid-slice

If a slice is turning into a rabbit hole, stop and cut. Trim the solution to the appetite, split the extra into a follow-up bead, or drop a non-goal. Do not let one slice absorb the whole project. File the follow-up so it is not lost:

```bash
bd create "Handle multi-currency totals" -t task --deps discovered-from:bd-c1.1 -p 2
```
