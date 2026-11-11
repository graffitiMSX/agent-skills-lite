# Agent Skills (lite)

Three free skills for AI coding agents that read a codebase the way a senior reviewer or a due-diligence auditor would: with evidence for every claim, and without changing a line.

| Skill | What it does | Full version |
|---|---|---|
| [design-review-lite](skills/design-review-lite/SKILL.md) | Six-lens review (architecture, backend, database, front-end, UX, security): the ten findings that matter most and a 0–10 grade per lens | [design-review](https://www.agensi.io/r/CODETECH?to=/skills/design-review%3Futm_source%3Dgithub%26utm_content%3Dreadme) |
| [discovery-audit-lite](skills/discovery-audit-lite/SKILL.md) | What the code really does versus what the pitch claims: implemented, partial, absent or undetermined, with evidence | [Reality Check bundle](https://www.agensi.io/r/CODETECH?to=/bundles/reality-check-what-your-app-really-is-and-how-it-grows-2-skills%3Futm_source%3Dgithub%26utm_content%3Dreadme) |
| [evolution-audit-lite](skills/evolution-audit-lite/SKILL.md) | How far the product is from SaaS, on-premise, white-label, a public API and an MCP server, and what to build next | [Reality Check bundle](https://www.agensi.io/r/CODETECH?to=/bundles/reality-check-what-your-app-really-is-and-how-it-grows-2-skills%3Futm_source%3Dgithub%26utm_content%3Dreadme) |

They follow the open [Agent Skills format](https://agentskills.io/specification), so they run in any agent that supports it: Claude Code, Codex, Gemini CLI, Cursor, GitHub Copilot and others. Each skill is a single `SKILL.md` with no scripts and no dependencies.

## Install

With the [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add graffitiMSX/agent-skills-lite
```

Or by hand: copy a folder from `skills/` into your agent's skills directory. For example, in Claude Code:

```bash
git clone https://github.com/graffitiMSX/agent-skills-lite.git
cp -r agent-skills-lite/skills/* ~/.claude/skills/
```

## Use

Ask your agent in plain words; the skill loads when the request matches.

```
Review this repo. Is it production-ready?
Here is our pitch: <paste>. What is actually built?
How far are we from an on-premise version and an MCP server?
```

Each skill writes one markdown report at the repo root. Run discovery-audit-lite before evolution-audit-lite: the roadmap starts from the baseline the first one writes.

## Lite or full?

The lite skills are a single pass by one agent. The full versions add what makes the result defensible in front of a client, an investor or a board:

| | Lite (free) | Full |
|---|---|---|
| Who reviews | One agent, one pass | Specialist agents per lens or area, in parallel |
| Depth | 6 lenses, 10 findings / brief claims + 5 basics / 7 targets | 57 graded aspects / 61 audit items plus domain checklists / every on-premise mode, MCP design, 8 moats |
| Scores | Judged by the model | Recomputed by a bundled script that rejects any grade breaking its cap |
| Output | One markdown file | Markdown report plus an interactive HTML page |
| Next steps | Up to you | A pipeline that plans the fixes, files GitHub issues, dispatches fix agents and re-reviews |

- **Ship-Ready**, seven skills from audit to merged fixes: https://www.agensi.io/r/CODETECH?to=/bundles/ship-ready-ai-code-audit-to-merged-fixes-10-skills%3Futm_source%3Dgithub%26utm_content%3Dreadme
- **Reality Check**, discovery-audit and evolution-audit: https://www.agensi.io/r/CODETECH?to=/bundles/reality-check-what-your-app-really-is-and-how-it-grows-2-skills%3Futm_source%3Dgithub%26utm_content%3Dreadme
- Free full skills that complete the pipeline: [plan-to-issues](https://www.agensi.io/r/CODETECH?to=/skills/plan-to-issues%3Futm_source%3Dgithub%26utm_content%3Dreadme), [dispatch-fixes](https://www.agensi.io/r/CODETECH?to=/skills/dispatch-fixes%3Futm_source%3Dgithub%26utm_content%3Dreadme), [design-memory](https://www.agensi.io/r/CODETECH?to=/skills/design-memory%3Futm_source%3Dgithub%26utm_content%3Dreadme)
- Everything by the author: https://www.agensi.io/r/CODETECH?to=/creators/rudolf-gutlich%3Futm_source%3Dgithub%26utm_content%3Dreadme

## Author

Rudolf Gutlich. [LinkedIn](https://www.linkedin.com/in/rgutlich) · [Agensi](https://www.agensi.io/r/CODETECH?to=/creators/rudolf-gutlich%3Futm_source%3Dgithub%26utm_content%3Dreadme)

## License

MIT, see [LICENSE](LICENSE).
