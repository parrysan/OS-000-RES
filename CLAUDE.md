---
title: "OS-000-RES — Research Skill Centre"
type: project-bootstrap
created: "2026-07-04"
---

# OS-000-RES — Research Skill Centre

> **Bootstrap order — read these in order before doing any work in this project:**
>
> 1. `~/.claude/CLAUDE.md` → `Open-Memory-Vault/system/identity/MASTER-PROMPT.md` — Phil's identity (auto-loaded via symlink in Claude Code; other tools should mirror this).
> 2. `~/AGENT.md` → `agent-config/AGENT.md` — global operating manual (work style, skills routing, secrets policy, layering rules in §2.10).
> 3. `Open-Memory-Vault/AGENTS.md` — vault operating contract (read **only** if you will write to the vault during this session).
> 4. `Open-Memory-Vault/projects/OS-000-RES/README.md` — durable project page (status, decisions, recent activity, vault-side context).
> 5. **This file (`CLAUDE.md`)** — project-specific overrides and live operational references (below).
>
> **The project's `CLAUDE.md` is a bootstrap manifest, not a knowledge dump.** It points at everything else. Durable knowledge lives in the vault project page. Do not duplicate.

---

## At a glance

- **Code**: `OS-000`
- **Name**: RES — Research Skill Centre
- **Stakeholder**: Phil (self)
- **Type**: `aios`
- **Status**: `active`
- **Priority**: `medium`
- **Revenue lane**: `4-aios`
- **Autonomy mode**: `autopilot` — research sweeps run end-to-end; Phil reviews at synthesis checkpoints.
- **Skill centre**: `Research` (code `res`)
- **Purpose** (one sentence): The **Research Skill Centre** — the AIOS centre of excellence for market, competitor, SERP, and deep-source intelligence: home for the research toolkit's standards, playbooks, and commissioned engagements (skill-centre pattern: `~/OG/ai-os/skill-centres.md`).
- **Last touched**: `2026-07-09`

---

## Where things live

| Resource | Location |
|---|---|
| **Code root** | this folder (`dev/OS-000-RES/`) |
| **Project docs** | `./docs/` (charter: `docs/research-domain-design.md` — pending, retrofit per skill-centres.md) |
| **Intelligence** | `./docs/intelligence/digests/` (og-res-video-yt cron writes durable per-video digests here) |
| **Vault project page** | `Open-Memory-Vault/projects/OS-000-RES/README.md` |
| **GitHub repo** | https://github.com/parrysan/OS-000-RES |
| **Research store** | [OG-Research/OS-000-RES](https://drive.google.com/drive/folders/14jvwSaJnOcCO_IRlav3dtpU5pW3JzjvE) (`research/`, `assets/`, `deliverables/`) — Drive folder ID stable; display-name rename pending |
| **External systems** | NotebookLM, Perplexity, skills.sh registry |

---

## Live references

- **Front door**: the global `research` router skill (existing — stays in the global library)
- **Toolkit (curated, global)**: `deep-research`, `research-deep-dive`, `research-notebooklm`, `research-video`, `og-res-video-yt`, `research-obsidian`, `research-etsy-monitor`, `daily-research`, `last30days`
- **Research Desk**: the ai-os floor sub-area (`~/OG/ai-os/AGENTS.md`) — this project is its home office
- **Credentials**: none stored here

---

## Tech stack

Markdown-first operating environment — standards, playbooks, commissioned-engagement folders. No web stack.

---

## Project-specific rules

- **Skills stay global.** This centre curates and sets standards for the research toolkit; it never absorbs the skills themselves (skill-centres.md contract).
- **Commissions in, briefings out.** Other centres commission research with a brief; deliverables land in the commissioning project's data folder, lessons land here in `docs/playbooks/`.

---

## Skills

- **Project-local skills**: none — uses global library
- **Most relevant global skills for this project**: `research`, `deep-research`, `research-notebooklm`, `last30days`, `meta-find-skills`

---

## Notes for the next session

Last action (2026-07-04): scaffolded as the Research Skill Centre home (cookie-cutter step 1). Next action: retrofit the charter (`docs/research-domain-design.md`, 7 sections per skill-centres.md) opportunistically when research work next runs through here. Open question: none.
