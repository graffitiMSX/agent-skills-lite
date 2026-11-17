---
name: break-it-lite
description: Adversarial single-pass test of a codebase against its own spec. Reads only the spec (PRD, README, OpenAPI file, tickets) and the code, and tries to break the backend, the front end and the infrastructure, reporting each break as a reproducible scenario with file:line evidence, numbered steps, expected versus actual and the test that would prove it. Use when the user asks to "break it", "try to break my app", red-team the code, find the inputs, races or deploy conditions that violate the spec, or check an implementation against its PRD or API contract.
license: MIT
metadata:
  author: Rudolf Gutlich
  version: "1.0"
  full-version: "https://www.agensi.io/r/CODETECH?to=/creators/rudolf-gutlich%3Futm_source%3Dgithub%26utm_content%3Dbreak-it-lite"
---

# Break It (lite)

A single pass whose only job is to break the application. It reads the code and never changes it. The output is one markdown file, `break-it-lite.md` at the repo root unless the user names another path, listing every break found with a way to reproduce it.

## Why start from zero

A reviewer that knows what the code is meant to do reads it as doing that, and defends it. So work only from the spec and the code. Ignore earlier reviews, the conversation so far and any explanation of the intent; if you already know them, set them aside and read the spec again as a stranger. When the user gives no spec, derive the claimed behaviour from the README, docs, route names and tests, and say so: those claims are weaker, but they are what users were promised.

## Rules

- **Read-only.** Never modify code, config or data, never run anything against production or any external endpoint, and never reproduce a secret; give only its location.
- **A break is a scenario.** Numbered steps from a stated precondition to an observable wrong outcome, with the file and line that cause it. "Consider adding validation" is advice, not a break. Drop anything you cannot reproduce on paper.
- **Falsify, do not excuse.** If the spec promises it and the code does not enforce it, it is broken, however unlikely the author thought the case was.
- **Label confidence:** Confirmed (you traced every step in the code), Likely (one step depends on timing or configuration you could not see) or Needs verification (say exactly what to check).
- **Write the proof, do not run it.** Each break comes with the smallest test that would fail on the current code and pass once fixed. This skill does not execute it; mark it "not executed".

## Steps

1. **List the claims.** From the spec, every promise about who may do what, what is stored, rejected, done once, limited, kept or recoverable. Add the invariants the code declares: unique constraints, states, comments, test names.
2. **Attack the three areas** with the moves below, one after another. For each claim, attack until it breaks or you can name the line that defends it.
3. **Keep every break you can reproduce**, ranked by severity: Critical (severe data loss, broad compromise, irreversible money), High (corruption, duplicate side effects, another user's or tenant's data, major outage), Medium (localized, recoverable), Low (defense in depth). Merge breaks with the same root cause.
4. **Give the verdict** per area: Broken (a Confirmed or Likely Critical or High break), Bent (only lesser breaks), Held (claims attacked, nothing broke), Not attacked. The overall verdict is the worst area's.

## Attacks

| Area | Try |
|---|---|
| Backend | Another user's or tenant's ID in path, body or query; no token, expired token, normal user on admin routes. Empty, huge, negative, unicode and extra fields (mass assignment). Two identical requests at once on every check-then-act. A retry after a timeout the server completed. A crash between two writes. What a caller can delete or overwrite, including bulk calls with an empty filter. Midnight, month end, daylight saving, client timestamps. Unbounded pages, uploads and loops. |
| Front end | Double click and Enter during a request; responses arriving out of order. Data from the previous user after logout and login. A session expiring mid-form. Rules only the client checks: send the request without the UI. Every request failing (401, 403, 404, 409, 500, timeout), empty lists. Deep links with no session or another user's ID; open redirects. Raw HTML sinks and user input in `href`. What the UI deletes, with what confirmation and undo. |
| Infra | Start without each required variable: refuse, fail later, or fall back to an unsafe default? Secrets in images, CI logs or bundles. Migrations run by every replica at start, not idempotent, or locking. Health checks that pass while a dependency is down. State on the container filesystem lost at restart. Two replicas: duplicate cron jobs, local caches, uploads on one disk. No resource limits. Backups claimed with no job or no restore. Deploys from unreviewed branches or with mutable tags; what a deploy that stops halfway leaves behind. |

## Output

```
# Break it (lite): <app>

Spec: <files, or "none supplied; claims derived from <sources>"> · Commit <sha> · <date>
Verdict: Broken (backend Broken, front end Bent, infra Held)

## Claims under attack
| Claim | Source | Result |
## Breaks
### B-01 [High] Any signed-in user can delete another user's list
- Area · Confidence
- Claim broken: the spec sentence, quoted, with its source
- Evidence: file:line, one per line when there are several
- Reproduction: numbered steps
- Expected · Actual
- Impact
- Proof test (not executed): the test in a code block
- Smallest fix
## Attacks that held
| Attack | Target | Why it held |
## What this did not cover
```

Keep each break under about 150 words plus its test. Close the reply, not the file, with the verdict, the breaks to fix first and what was not covered.

## Going further

This is the single-pass, read-only version. The full **break-it** skill runs three adversarial agents in parallel, one each for backend, front end and infrastructure, each handed only the spec and the code. With your go-ahead they run every proof test in a throwaway copy outside your repo and paste the output, a bundled script rejects any break without reproduction steps, evidence or a proof status and computes the verdict, and the report feeds a pipeline that plans the fixes, files GitHub issues and dispatches fix agents. Everything by the author: https://www.agensi.io/r/CODETECH?to=/creators/rudolf-gutlich%3Futm_source%3Dgithub%26utm_content%3Dbreak-it-lite
