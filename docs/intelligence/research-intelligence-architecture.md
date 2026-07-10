# Research intelligence architecture — design

**Date**: 2026-07-10 · **Status**: approved by Phil (brainstorm + og-plan-roast) · **Home**: OS-000-RES
**Instance of**: the generic intelligence loop (`~/OG/ai-os/skill-centres.md` §Intelligence loop).
Generalizes `seo-scout-spec.md` from a single-centre scout into the OS-wide research engine.

## Problem

The digest/postable convention already exists in OS-009's skill-centre portal (`skill_centres.py`,
per-centre Intelligence tabs) but nothing real feeds it, and even filled in, a markdown file in a
repo has no "do something about it" step. A council review (`og-plan-roast`, 2026-07-10) confirmed
the failure mode: unread/unapplied output, not insufficient sourcing. Verdict was RESHAPE — reuse
the proven plumbing, replace the empty middle, close the feedback loop before widening the funnel.

## Architecture

**1. Sources (multi-origin intake)**
- **GitHub** — tagged repo registry (seed: `obra/superpowers`, `multica-ai/andrej-karpathy-skills`,
  `mattpocock/skills`, `affaan-m/everything-claude-code`, `VoltAgent/awesome-claude-skills`,
  `ComposioHQ/awesome-claude-skills`, `coreyhaines31/marketingskills`), checked weekly via `gh api`
  against last-seen SHA per repo.
- **YouTube** — tagged channel registry (name, URL, tags e.g. `ai-strategy`, `seo`, `revenue-ideas`),
  managed via a channel-input UI on the OS-009 Research tab; checked weekly against last-seen video ID.
- **NotebookLM** — a saved standing prompt, **Phil-triggered** (portal button or session-run), never
  cron-automated. No consumer API exists for free/Plus tiers; automating it would mean fragile
  unofficial scraping — exactly the maintenance burden this system exists to avoid.

**2. Curation — one LLM pass, all sources, per sweep**
Reads everything new since last run across all three sources, filters hard to 3–7 sourced, dated
findings (never a bulk dump). For each survivor, assigns:
- **one home target** (single ownership — see Dispatch)
- a proposed action: `none | playbook edit | domain-law change | tool/skill adoption | commission
  another centre`

**3. Dispatch — single owner + commissioning, not multi-tag**
Every finding lands in exactly one target's Intelligence section. Cross-cutting findings use the
doctrine's existing **Commissioning** channel (centre X hands centre Y a brief) rather than new
multi-tag plumbing. Targets are not limited to the four named skill centres (SEO/AEO, Design,
Engineering, Video) — any project onboarded via the cookie-cutter (below) is a valid target, e.g.
`OG-026-Pet-Portraits-POD`, `OG-015-Tartak`.

**4. Apply loop — the piece that was missing**
Findings become **pending items**, not just digest lines, reusing the exact pending → decide → apply
pattern already proven in OS-009 for Dream Cycle review and the Memory Inbox
(`review_queue.py`, `memory_inbox.py`): Phil reviews in the portal, accepts or rejects, and
acceptance **triggers the real change** — a playbook commit, a skill file drop, a domain-law edit —
not a status flip. The digest stays append-only history; the pending queue is the actionable surface.

**5. Archive — Google Doc per finding**
One Doc per finding (not per weekly digest bundle), published via `gws docs` into the *dispatched*
target's own Drive `research/` folder, following the exact convention `og-res-video-yt` already uses
for video intake (`**Doc:** <url>` folded into the digest, naming `<source-skill> · YYYY-MM-DD ·
<title>`). This is what turns the archive into something actually searchable later — each Doc is one
atomic, greppable unit, findable via `gws drive files list` or Drive's own full-text search.

**Known prerequisite gap**: only SEO (`OG-Research/OS-000-SEO`) and Research
(`OG-Research/OS-000-RES`) currently have a Drive `research/` folder. Engineering has none, Design's
Drive folder is for brand assets not research, Video isn't scaffolded as a project yet. Each target
needs its own `OG-Research/<code>/` folder created before dispatch can archive into it — cheap,
one-time, sequenced into rollout below.

## Provisioning — cookie-cutter via `/og-project`, not a new skill

Extends the existing `/og-project` skill rather than adding a new command — Phil is unlikely to
invoke this himself; it needs to be discoverable *to an agent* bootstrapping a project, which means
living in the same place every session already reads (`CLAUDE.md`, via the standard layered bootstrap).

- **New project** → scaffold includes the research facility by default: empty `config/sources.json`,
  `docs/intelligence/` folder, a line in the generated `CLAUDE.md`'s "Where things live" table.
  Structural only — free, no Drive folder created yet.
- **Existing project** → two new flags on `/og-project`:
  - `--add-dashboard` — brings `CLAUDE.md` up to the canonical template (the `## At a glance` block
    and required structure OS-009 needs to parse it). Real, observed gap today:
    `OS-016-Video-OpenMontage` currently fails this parse and doesn't render on the board. Includes
    `--add-research` by default (Phil's stated coupling: a dashboarded project gets research by
    default).
  - `--add-research` — adds just the research facility scaffold, for a project that already
    dashboards fine but lacks research.
  - `--health` (existing flag) stays **read-only** — detects and reports which of the above a project
    is missing, same as it already flags other template drift. The `--add-*` flags are the mutating
    fix, git-committed to that project's own repo like any other `/og-project` edit.
  - Expensive step (creating the actual `OG-Research/<code>/` Drive folder) happens lazily, on first
    real source registration — not eagerly for every project that gets the structural scaffold.

## Execution

Weekly Hermes cron on the always-on Mac Studio (same tier as the two live crons: Vault Auto-Commit,
Video Inbox Auto-Ingest) — matches the pattern already proven, unlike the SEO scout spec which was
written but never installed. NotebookLM sweep is excluded from the cron; Phil triggers it separately.

## Rollout order (sequenced, not parallel — per roast verdict)

1. **GitHub only.** Wire curation → dispatch → apply end-to-end against the 7 seed repos. Confirm
   Phil is actually opening and acting on pending items in the portal for 3–4 weeks before widening.
2. **YouTube.** Once the channel-tagging UI exists and channels are registered.
3. **NotebookLM manual trigger.** Gated on step 1 proving the review habit is real.
4. **`/og-project` flags + per-centre Drive folders.** Built alongside so Engineering/Video/Design
   become valid dispatch targets, not just SEO/Research.

## Roast verdict (2026-07-10, `og-plan-roast`)

RESHAPE, high confidence. Biggest risk named by 4/5 council personas independently: a second unread
folder, repeating the exact failure already observed once (`seo-scout-spec.md`, written, never
installed). Cheapest 48-hour test specified: run one GitHub sweep by hand, land the digest as a card
on the OS-009 dashboard, confirm Phil actually opens and acts on it — before automating anything.
Scores: `Contrarian 3/10 · Expansionist 9/10 · Logician 6/10 · Researcher 6/10 · Buyer 4/10 (8/10 if
unbundled)`.

## Out of scope for this design

- NotebookLM standing-prompt/monthly report loop as a second automated track — descoped to manual
  trigger per the roast.
- Multi-tag dispatch (a finding appearing in more than one centre at once) — using the doctrine's
  existing Commissioning channel instead.
- Building out Video's home repo (`OS-000-VID` doesn't exist yet) — a prerequisite noted, not solved
  here.
