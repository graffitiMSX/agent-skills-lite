---
name: evolution-audit-lite
description: Assess how far a product is from SaaS, on-premise, white-label, a public API, an MCP server for AI agents and AI features, starting from what the code really does, and end with the five next initiatives and plain answers on what to sell, build next and not build. Use when the user asks "how far are we from on-premise / white-label / MCP", "what should we build next", or wants a roadmap grounded in the code rather than the slide deck.
license: MIT
metadata:
  author: Rudolf Gutlich
  version: "1.0"
  full-version: "https://www.agensi.io/skills"
---

# Evolution Audit (lite)

Plans the next steps of a product from where it really is. Reads a baseline and the repository, and writes `evolution-audit-lite.md` at the repo root unless the user names another path: a readiness table, the five initiatives worth doing next and eight plain answers.

Inspection and planning only: no code, configuration or external state changes, and no full rewrite unless the code shows incremental change cannot work.

## Steps

1. **Load the baseline.** Use a `discovery-audit-lite.md` (or a full discovery audit) when there is one. Without one, offer to run discovery-audit-lite first; planning without a baseline guesses at the starting point. If the user declines, read the README, manifests, routes and models and say in the report that the baseline is informal.
2. **Ask what the repo cannot show** and use only what the user answers: target customers, pilots, pricing ideas, competitors, deadlines, team size. Missing answers stay unknown; never invent them. When the user cannot answer, list the questions in the report header and carry on. Market and competitor statements are Inferences unless the user supplied them.
3. **Define the product's shape** in one line before grading: a tool sold to organizations, a two-sided marketplace, an internal system. Say who the tenant is and what "SaaS" means for it (for a marketplace, usually the seller's company and the seller's subscription). Then **assess readiness** for each target below, marked `[x]` ready, `[~]` partial, `[ ]` not ready or `[?]` unknown, each with evidence from the code and the blocker that matters most.
4. **Pick five initiatives** and answer the eight questions, then write the report. Order them P0 gaps first, then exploitable security P1s, then revenue and the rest; a later step never hides a P0.

## Targets

| Target | Check |
|---|---|
| SaaS | Data isolated per tenant, settings per organization, plans and usage limits, billing, automated onboarding, central monitoring. |
| On-premise, connected | Runs on the customer's infrastructure (containers, config by environment, their database and storage, backups, upgrades and rollback, docs), still allowed to call authorised external services. |
| On-premise, air-gapped | Works with no internet at all. List every external dependency (identity, email, storage, payments, AI APIs) and whether it breaks this mode. Never assume an external identity or signing provider works offline. |
| White-label | Branding, own domain, per-tenant templates and feature switches, admin split between vendor and customer. Can it share one code base with SaaS and on-premise? |
| Public API | Consistent contracts, per-organization keys, versioning, OpenAPI that matches the routes, signed webhooks with retry and idempotency, rate limits. |
| MCP server | Which operations agents could call, each classed read, write or execute. Anything with legal or financial effect needs explicit human approval enforced by the server, not by the agent's instructions; tenant permissions apply exactly as in the app; the server calls existing services and never re-implements business rules. |
| AI features | Where AI helps (extraction, triage, summaries, assistants), each marked probabilistic or deterministic. Validation, security and business rules stay deterministic. |

## Report

```
# Evolution audit (lite): <product>

Baseline: <report path and commit, or "informal">. Audited commit, date, facts the user supplied and those left unknown.

## Readiness
| Target | State | Evidence | Main blocker | Effort (S/M/L; two values when the scope splits, e.g. "M one brand / L many") |

## External dependencies
| Dependency | Used for | Breaks on-premise connected? | Breaks air-gapped? |

## MCP candidates
| Tool | Kind | Approval | Backed by |

Approval is `none` (read only), `user` (the caller confirms), `human` (a person approves outside the agent, enforced by the server) or `excluded` (not offered to agents now).

## Next five initiatives
### 1. Make every query tenant-scoped
- What: what gets built or changed
- Why: the gap it closes, citing the baseline
- Depends on · Complexity (low/medium/high) · Risk
- Done when: an objective, checkable criterion

## Answers
1. Is the technical base fit to sell?
2. What is the biggest risk today?
3. Which capability has the most potential to differentiate?
4. Is it ready for enterprise customers?
5. What effort does on-premise take?
6. What effort does an MCP server take?
7. What should the next, highest-return delivery be?
8. What should not be built now?
```

Write the report in the baseline's language. Keep each initiative under about 120 words. Close the reply, not the file, with the eight answers and the first two initiatives.

## Going further

This lite version gives a readiness snapshot and five initiatives. The full **evolution-audit** skill assesses every on-premise mode item by item (connected, controlled dependencies, air-gapped), designs the MCP server with a tool table, approvals and an agent-security checklist, grades eight competitive moats on evidence, and builds a phased eight-stage roadmap where every initiative cites the baseline gap it closes. A bundled script refuses missing modes, unapproved execute tools and dependency loops. It comes with **discovery-audit** in the Reality Check bundle: https://www.agensi.io/skills
