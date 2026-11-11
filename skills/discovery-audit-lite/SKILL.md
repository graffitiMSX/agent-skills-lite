---
name: discovery-audit-lite
description: Check what an application really does against what its pitch, brief or README claims, marking each claimed capability implemented, partial, absent or undetermined from end-to-end evidence in the code, never from a class or route merely existing. Use when the user pastes a product description and asks "what is actually built", "is this ready to sell", wants a quick technical due diligence, or is inheriting a codebase.
license: MIT
metadata:
  author: Rudolf Gutlich
  version: "1.0"
  full-version: "https://www.agensi.io/skills"
---

# Discovery Audit (lite)

Measures the code against the claims. Reads a brief (pitch, product description, roadmap, README) and the repository, and writes `discovery-audit-lite.md` at the repo root unless the user names another path: a capability matrix, the claims the code contradicts, four maturity ratings and the most serious findings.

Inspection only: it never changes files, configuration, dependencies or data, never calls external services that could change data, and never reproduces a secret.

## The one rule

**A capability counts as implemented only when the code shows it working end to end:** an entry point (route, screen, command, job), the logic behind it, persistence or the external call it needs, and a test or another trace that it runs. A class, an interface, a route stub, a feature flag or a README line is not enough. A trace can be a test, a CI job that runs it, a migration and the code that writes to it, or a deployed health check; in a repo with no front-end tests, a back-end test of the API the screen calls counts for the screen. This audit reads code and runs nothing, so say in the report that a test existing stands in for it running. Most audits go wrong here, and the gap between "there is a file called `BillingService`" and "customers can be billed" is what the reader is paying attention to.

| Mark | Meaning |
|---|---|
| `[x]` | Implemented and shown to work end to end |
| `[~]` | Partial: some of the path exists, say which part is missing |
| `[ ]` | Not implemented, or scaffold only |
| `[?]` | Could not be determined: say what would settle it |

When the code does something but contradicts the claim (a "credit bureau check" that invents its data, an "AI analysis" that is a fixed rule set), mark it `[ ]` or `[~]`, start the gap with **Contradicts the brief**, and list it under claims against code.

## Steps

1. **Take the brief.** Use what the user pasted; if there is none, ask for one, since the audit measures the code against it. Treat every sentence as a claim to test.
2. **List the capabilities.** Turn the brief into numbered capabilities, one per claim (`CAP-01`, `CAP-02`…); group claims packed into one sentence when they share a code path, aiming at 15 to 30 rows. A brief sentence about the code's state rather than the product ("built with X", "the API is at /api") goes to claims against code, not to a row. Add five that every product needs whether the brief mentions them or not: authentication, authorization per organization or tenant, error handling and logs, automated tests, and deployment and configuration per environment. Show the list to the user before auditing.
3. **Trace each capability** through entry point, logic, persistence and tests, and mark it. Cite the files that prove an `[x]` or `[~]`.
4. **Collect claims against code:** every place where the brief, README or docs say something the code does not do. List what the code does that the brief never mentions separately, under "In the code, not in the brief".
5. **Write findings** for the gaps that matter, at most eight, each with a priority: P0 act now (data loss, security breach, a core claim that is false), P1 high, P2 medium, P3 low. Mark each one Fact (seen in the code) or Inference.
6. **Rate maturity** and write the report.

## Report

```
# Discovery audit (lite): <product>

Repo, commit <sha> (and whether the working tree differs), date, brief sources, paths left out.

## Summary
One paragraph on what the product really is today, then:
- Technical maturity: <level> — reason citing CAP rows
- Functional maturity: <level> — …
- Security maturity: <level> — …
- Commercial maturity: <level> — …
Levels: absent, prototype, functional, production-ready, proven.

## Capability matrix
| ID | Capability | State | Evidence | Gap | Priority |
Counts: x implemented, y partial, z absent, w undetermined.

## Claims against code
| Claim | Source | What the code shows |

## In the code, not in the brief

## Findings
### P1 Password reset tokens never expire
- Basis: Fact
- Evidence: file:line, one per line
- Scenario:
- Impact:
- Recommendation:
- How to verify:

## Technical inventory
Languages, frameworks, data stores, integrations, infrastructure, each with the file that proves it.

## What this audit did not cover
```

Write the report in the brief's language. Close the reply, not the file, with the counts, the P0 and P1 findings and the claims the code contradicts.

## Going further

This lite version audits the claims in the brief plus five basics. The full **discovery-audit** skill audits 61 items across architecture, identity and access, security and compliance, data and storage and the API surface, adds domain checklists, runs one auditor agent per area in parallel, has a bundled script check every row and compute the matrix, and renders an interactive HTML report. Its companion **evolution-audit** turns the baseline into a roadmap for SaaS, on-premise, white-label, a public API and an MCP server. Both are in the Reality Check bundle: https://www.agensi.io/skills
