---
name: design-review-lite
description: Quick evidence-based design review of a codebase through six lenses (architecture, backend reliability, database, front-end, UX and security) in a single pass, ending in the ten findings that matter most and a 0–10 grade per lens. Use when the user asks for a quick architecture, reliability or security review, "is this production-ready", "what would break first", or a first look at an unfamiliar repo.
license: MIT
metadata:
  author: Rudolf Gutlich
  version: "1.0"
  full-version: "https://www.agensi.io/skills"
---

# Design Review (lite)

A single-pass review of an application through six lenses. It reads the code and never changes it. The output is one markdown report, `design-review-lite.md` at the repo root unless the user names another path, with a grade per lens and the ten findings most worth fixing.

## Rules

- **Read-only.** Never modify code, config or data, and never call production endpoints.
- **Evidence or it did not happen.** Every finding cites a file and, where it can, a line or symbol. Quote the minimum and never reproduce a secret; give only its location.
- **Label confidence:** Confirmed (seen in the code), Likely (strong inference) or Needs verification (say exactly what to check). When the code path is seen but the failure depends on timing, say both: "Confirmed path, Likely race".
- **A happy path proves nothing.** Assume crashes before and after every side effect, duplicate and reordered requests, timeouts with unknown outcome, concurrent users and another tenant with a valid account.
- **Smallest safe fix first.** No rewrites unless incremental change cannot address the risk.
- **Report strengths too**, not only defects.

## Steps

1. **Orient.** Read the README, the dependency manifests, the entry points, the route definitions and the data models. In a large repo you will not read everything: read the code on the paths where money, identity and data change hands, and search the rest. Write a five-line system map: runtime, frameworks, data stores, external services, how requests and jobs flow. Skip the front-end and UX lenses when there is no client code, and the database lens when the app keeps no data of its own, and say so.
2. **Run the six lenses**, one after another, with the questions below. Note candidate findings as you go.
3. **Keep the ten that matter most.** Rank by realistic impact and likelihood, not by how interesting they are. List the runners-up in one line each under "Also noted"; they still count toward the grades. Each finding has one lens, the one where the fix lives; name others it touches in the text. Severity: Critical (severe data loss, broad compromise, irreversible financial impact), High (corruption, duplicate side effects, cross-tenant exposure, major outage under realistic conditions), Medium (localized, recoverable), Low (defense in depth).
4. **Grade each lens** from 0 to 10 with the rubric below, then write the report.

## Lenses

| Lens | Ask |
|---|---|
| Architecture | Are module boundaries real or only folders? Who owns each piece of data? Where are the single points of failure? Can a deploy break running requests or jobs? Can you see failures in logs and metrics? |
| Backend | Which mutations can be retried (client, gateway, queue, scheduler), and is each one idempotent? Where does the code write to two systems (database and queue, payment, email) with a failure window between them? Which read-modify-write or check-then-act paths race? Do outbound calls have timeouts and bounded retries? |
| Database | Is every business invariant backed by a constraint, not only by application code? Do the indexes match the queries the code actually runs? Any unbounded reads or N+1 loops? Would the pending migrations lock a large table? |
| Front-end | Can a double click submit twice? Do loading, error and empty states exist for every request? Can a slow response overwrite a newer one? Is anything sensitive kept in local storage? |
| UX | Walk the main task from start to finish. Where does the user get stuck, lose work or not know what happened? Do error messages say how to recover? |
| Security | Does every endpoint check who the caller is and whether they may touch this object (IDOR)? Is every query scoped to the tenant? Are webhooks signature-checked and replay-protected? Any secrets in the repo, logs or client bundle? Any injection, SSRF or unsafe upload path? |

## Rubric

| Grade | Meaning |
|---|---|
| 9–10 | Exemplary. Nothing above Low; controls are tested and observable. |
| 7–8 | Solid. Medium or Low findings only. |
| 5–6 | Adequate with gaps. One High, or several Medium that compound. |
| 3–4 | Weak. Several High, or a Critical with partial mitigation. |
| 0–2 | Unsafe or absent. An unmitigated Critical, or the control does not exist. |

Three or more Medium findings in one lens count as compounding (5–6). A Confirmed or Likely Critical finding caps its lens at 3, a High at 6. Grade what you looked at; write `not assessed` for a lens you could not inspect. Show each gauge as ten segments: `▰▰▰▰▰▰▱▱▱▱ 6/10`. The overall score is the mean of the graded lenses, to one decimal, under the same caps, and says which lenses it covers and whether a cap changed it.

## Report

```
# Design review (lite): <app>

Scope: commit <sha> (or "working tree"), <date>, paths left out and why.
Overall: ▰▰▰▰▰▱▱▱▱▱ 5.2/10 (6 lenses; mean 5.2, the cap of 6 from High findings does not bind)

## System map
## Scorecard
| Lens | Grade | Gauge | Why | Findings |
## Top findings
### F-01 [High] Checkout can charge twice on retry
- Lens · Confidence
- Evidence: file:line, one per line when there are several
- Scenario: numbered steps a reader can reproduce
- Impact · Smallest safe fix · How to verify the fix
### Also noted
- One line per runner-up, with its lens and evidence
## Strengths
## What this review did not cover
```

Keep each finding under about 120 words. A finding about something missing (no rate limiting, no metrics) cites where the control should be and the search that found nothing. Close the reply, not the file, with the overall score, the three actions to take first and what was not covered.

## Going further

This is the single-pass version. The full **design-review** skill runs six specialist reviewer agents in parallel, grades 57 aspects instead of six lenses, has a bundled script recompute every score and reject a grade that breaks its cap, maps security to the OWASP Top 10, analyses every data store under failure (CAP), respects the decisions your team already recorded, and renders an interactive HTML report. It is the first stage of a pipeline that plans the fixes, files them as GitHub issues, dispatches fix agents and re-reviews to show how the score moved: https://www.agensi.io/skills
