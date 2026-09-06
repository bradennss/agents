---
name: pen-testing
description: >
  Use when the user wants to find, exploit, and fix security vulnerabilities in a project
  they own or are authorized to test. Covers threat modeling, source-to-sink code review,
  proving each bug in a safe local environment, fixing the root cause, and adding a regression
  test. Trigger on requests like "pentest my app", "find security vulnerabilities", "do a
  security audit", "is my code safe to ship", "check for SQL injection or XSS", "review this
  API for auth or IDOR bugs", "harden this service", or "fix this security issue", even when
  the user does not say "pen-test" or "security" directly.
metadata:
  version: "1.0"
  domain: application-security
---

# Pen-testing your own projects

Find real, exploitable vulnerabilities in a project the user owns, prove each one, then fix the
root cause and add a test so it stays fixed. Favor a small set of confirmed, high-impact findings
over a long list of guesses.

## Authorization gate

Before touching anything, confirm two things:

1. The user owns the target or is authorized to test it. This skill is for the user's own code,
   staging, or local environments. If the target is a third party or production system you are not
   sure about, stop and ask.
2. You have a safe place to run exploits. Use a local checkout, a throwaway database, and test
   credentials. Never run destructive or noisy attacks against production or shared data.

If either is unclear, ask before proceeding.

## Workflow

Work top to bottom. Skip steps only when the user asked for a narrow, specific check.

### 1. Set scope

Pin down the target and depth:

- What to review: a single endpoint, a module, a diff, or the whole repo.
- Stack: languages, frameworks, datastores, and how the app is deployed.
- Depth: a quick pass on the top risks, or a full audit across every class.
- What matters most: the assets an attacker would want, like user data, money, or admin access.

State the plan in one or two sentences before you dig in.

### 2. Map the system

You cannot find bugs in code you do not understand. Build a picture first:

- Entry points where outside data enters: HTTP routes, GraphQL resolvers, webhooks, message
  queues, CLI args, file uploads, env vars, and any input read from other services.
- Trust boundaries: where data crosses from untrusted to trusted, and where one user's request
  can reach another user's data.
- Sensitive sinks: SQL and other queries, shell and process calls, filesystem paths, outbound
  HTTP, template rendering, deserializers, and anything that runs code.
- Authn and authz: how the app knows who you are and what checks gate each action.
- Secrets and config: how credentials, keys, and tokens are stored and loaded.
- Dependencies: direct packages, lockfiles, and anything pulled at build or runtime.

Read `references/vulnerability-catalog.md` for the full list of classes and what to look for in
each. Load it once at the start of a full audit.

### 3. Hunt by vulnerability class

For each class in scope, trace attacker-controlled data from a source to a dangerous sink:

1. Find the sources: every value an attacker can set.
2. Follow the data through assignments, function calls, and module boundaries.
3. Check what sits between source and sink: validation, encoding, parameterization, or a framework
   protection. Ask whether it actually stops the attack or can be bypassed.
4. Give a preliminary verdict: vulnerable, likely vulnerable, or safe.

Cover access control and business logic too, since those have no single sink. Check that every
sensitive route enforces authn and authz, that object IDs cannot be swapped to reach another user
(IDOR), and that multi-step flows cannot be raced or reordered.

Run tooling when it helps and it is available, but treat results as leads to confirm by hand, not
as findings. Prefer running a scanner through `npx`, `uvx`, `pipx run`, or a container so you do
not have to install it. See `references/proving-and-fixing.md` for the tools and how to use them.

### 4. Prove it

A finding is not real until you show it. Do not report on suspicion. For each candidate:

- Stand up a safe local instance with a throwaway datastore and test accounts.
- Build the smallest request or input that triggers the bug and show the result: the leaked row,
  the reflected script, the command output, the access you should not have.
- Save the exact reproduction so it can be rerun and later used as a regression test.

If you truly cannot run the code, say the finding is unverified and explain what is missing,
rather than claiming it as confirmed.

### 5. Triage and cut false positives

Act as an adversarial reviewer of your own candidates before reporting. For each one, answer:

- Reachability: is the source really attacker-controlled, and is the path reachable from a live
  entry point, not dead or internal-only code?
- Sanitization: did you miss a guard upstream, in middleware, or in the framework?
- Exploitability: does the input reach the sink in a form that actually fires, and is the impact
  real rather than hypothetical?

Keep a finding only when it is confirmed or you proved it. Drop the rest, or mark them unverified
with a note on what is needed to decide.

### 6. Fix the root cause

Fix the underlying flaw, not the one payload you tested:

- Prefer the safe primitive over filtering: parameterized queries, safe APIs, output encoding at
  the sink, allowlist validation, and framework protections turned on.
- Enforce authz at every entry point and deny by default, rather than patching one route.
- When the same flaw appears in several places, fix all of them and remove the unsafe pattern so
  there is one safe way left.
- Never paper over a symptom you know how to fix properly.

After fixing, rerun the reproduction from step 4 and show that the attack now fails. Then add a
regression test that encodes the attack, so a future change that reopens the hole fails CI.

### 7. Report

Give the user a short, ranked report. Lead with the worst issue and the fix. Keep it evidence
first, not a wall of theory. See the finding format below and the report template in
`references/proving-and-fixing.md`.

## Severity

| Severity | Examples |
|----------|----------|
| Critical | Remote code execution, authentication bypass, unauthenticated access to sensitive data |
| High | SQL injection, SSRF, IDOR exposing other users' data, stored XSS, privilege escalation |
| Medium | Reflected XSS, CSRF on state changes, path traversal, insecure deserialization behind auth |
| Low | Information disclosure, open redirect, weak crypto, missing cookie flags |
| Info | Missing security headers, verbose errors, defense-in-depth gaps |

## Finding format

```
[SEVERITY] <Vulnerability class>  (confirmed | unverified)
Location: <file>:<line> or <endpoint>
What: one sentence on the flaw.
Impact: what an attacker gains.
Proof: the request or input used, and what came back.
Fix: the specific change, at the root cause.
Test: the regression test that now guards it.
```

## Key principles

- Prove before you report. Evidence over assertion.
- One confirmed critical bug beats ten unverified maybes.
- Fix the cause, apply the fix everywhere it belongs, and lock it with a test.
- Be precise. Exact files, lines, and payloads, never approximations.
- Stay in scope and safe. Own the target, use throwaway data, leave production alone.

## References

- `references/vulnerability-catalog.md`: the vulnerability classes, where they hide across common
  stacks, how to spot them, and the root-cause fix for each. Load at the start of a full audit, or
  load the relevant sections for a targeted check.
- `references/proving-and-fixing.md`: building a safe reproduction environment, optional scanners
  and how to run them, writing regression tests, and the report template.
