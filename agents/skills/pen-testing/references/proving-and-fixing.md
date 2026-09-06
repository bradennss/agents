# Proving and fixing

How to stand up a safe target, prove each bug with a real exploit, run optional scanners, lock the
fix with a regression test, and write the report.

## Build a safe reproduction environment

Never test against production or shared data. Set up a local instance you can attack freely:

1. Check out the code locally and read the run instructions in the README, `Makefile`, or compose
   file.
2. Start the app with a throwaway datastore. Prefer a disposable database in a container or a fresh
   local instance seeded with fake data. Do not point it at any real database.
3. Create at least two test users, so you can test one user reaching another user's data.
4. Load fake but realistic data. Never use real personal data, secrets, or production dumps.
5. Set test credentials and keys through the environment, and keep them out of the repo.

Keep the whole setup reproducible, ideally as a script or compose file, so you can rebuild it and
rerun every exploit later.

## Prove the exploit

For each candidate, drive the running app and capture evidence:

- Web and API: use `curl`, an HTTP client, or the `agent-browser` CLI for flows that need a real
  browser and session. Show the exact request and the response that proves impact.
- Injection: show the leaked data, the command output, or the error that confirms the parser saw
  your payload.
- Access control and IDOR: log in as user A, capture a request, replay it as user B or with B's
  object ID, and show A's data coming back.
- CLI and library: write the smallest program or command that triggers the bug and show the output.

Save each reproduction as a script or a saved request. That artifact becomes the regression test in
the fix step. If the code cannot be run at all, mark the finding unverified and say what is missing.

## Optional scanners

Scanners find leads fast, but every hit needs manual confirmation with a real exploit. Treat their
output as a to-do list, not as findings. Run them without a global install where possible:

- Static analysis: `semgrep` finds injection, XSS, and misconfig patterns across many languages.
  Run with `uvx semgrep scan --config auto`. `bandit` covers Python (`uvx bandit -r .`).
- Secret scanning: `gitleaks detect` or `trufflehog` find committed secrets. Scan history, not just
  the working tree.
- Dependency and CVE scanning: `osv-scanner scan .` (via its container or binary), `npm audit`,
  `uvx pip-audit`, or `trivy fs .` for a repo and its lockfiles.

If a tool is not installed and cannot be run through `npx`, `uvx`, `pipx run`, or a container, say
so and fall back to manual review rather than skipping the class.

## Fix at the root, then lock it

After the fix from the main workflow, prove it holds:

1. Rerun the exact reproduction from the proof step and show the attack now fails.
2. Add a regression test that encodes the attack so a future change that reopens the hole fails CI.
   Put it with the project's existing tests and match their style:
   - API bug: a test that sends the malicious request and asserts a rejection or a scoped response.
   - IDOR: a test where user B requests A's object and gets 403 or 404.
   - Injection: a test that feeds the payload and asserts it is treated as data, not executed.
   - Dependency: pin the fixed version and let the CVE scanner run in CI.
3. Run the full test suite so the fix did not break anything.

## Report template

Keep it short and ranked, worst first. Lead with evidence and the fix.

```markdown
# Security review: <target>
Date: <date>
Scope: <what was reviewed> (<languages / frameworks>)

## Summary
<Two or three sentences: how many confirmed findings by severity, and the single worst issue.>

## Findings

### [SEVERITY] <Vulnerability class> (confirmed | unverified)
Location: <file>:<line> or <endpoint>
What: <one sentence on the flaw>
Impact: <what an attacker gains>
Proof: <the request or input used, and what came back>
Fix: <the change made, at the root cause>
Test: <the regression test that now guards it>

<repeat per finding, ordered by severity>

## Fix priority
<Ordered list: what to fix first and why.>

## Notes
<Unverified leads, areas not covered, and anything the user should follow up on.>
```
