# Research intelligence — v2 design (agent-curated, vault-backed, one checkpoint)

**Date**: 2026-07-11 · **Status**: approved in conversation (Phil, 2026-07-11) · **Home**: OS-000-RES
**Revises**: `research-intelligence-architecture.md` (the Phase-1 build). This document supersedes the
parts of Phase 1 noted under "What this changes" below; the sources layer and the working sweep
engine carry over unchanged.

## Why revise

Phase 1 made Phil triage every finding by hand — assign tags, pick a target, accept/reject per item,
across a portal Inbound plus per-centre pending lists. In use, that's too much admin and too many
places to look. The v2 shift, in Phil's words:

> "The agent takes care of the dissemination... the only thing I need to keep an eye on is the
> inbound source content in the feed. It needs to be carefully curated and end up in our memory
> vault, alongside where it makes the most sense, so findings are discoverable as and when needed."

So the model moves from **human triages everything** to **agent curates, tags, routes, and drafts;
human approves once; approval fans the finding into the durable memory.** Phil's one job is pointing
research at good sources.

## The model in one line

Sources → agent curation (judge · tag · route · draft social) → **one review checkpoint in the
Morning Report** → on approval: a curated write into the **Open Memory Vault** (single source of
truth) → skill-centre Intelligence sections are **tag-queries** over the vault; social-worthy items
flow to the existing Social queue.

## Components

### 1. Sources — Phil's only management job (unchanged)

The Feeds screen (OS-009 portal, Research tab) stays as built: add/remove/tag sources, GitHub +
YouTube, with the clean row + detail-popup UI. This is the one surface Phil actively curates.
Everything downstream is the agent's job.

### 2. Curation, routing, tagging, social — one agent pass

For each new item a source surfaces, the research agent produces, in a single pass:

- **Gem judgement** — worth Phil's attention at all? Most items are not; do not force findings.
- **Topics (tags)** — `seo`, `eng`, `design`, `vid`, `res`, plus free topic tags. These tags are the
  retrieval key for everything downstream (see §5) — good tagging is the linchpin of the whole system.
- **Target centre(s)** — one, several, or all. **The agent decides routing now** (Phase 1 had the
  feed's configured target drive this and the LLM forbidden from guessing; that reverses — see
  "What this changes"). A finding can legitimately belong to several centres at once.
- **A clear, concise internal summary** — written plain and scannable so it reads well in the
  Morning Report in-tray. This is a *writing standard baked into the curation prompt*, not the
  `meta-humanizer` skill (which is scoped to external copy — see §6).
- **A social draft, if warranted** — a "social lens" step in the same pass asks "worth a post?" and,
  if yes, drafts a short post. One read, two verdicts. This is the "Social Media Agent" as a *role /
  pipeline step*, not a separate always-on daemon re-scanning the same feed (§6).

### 3. Review — one checkpoint, injected into the Morning Report

Findings do **not** get their own destination. They inject into the **existing Morning Report
stream** (OS-009 dashboard) as a filterable **Research** category, alongside the self-improvement
proposals and memory-inbox items already there. This keeps Phil in one place.

Reuses the established Morning Report card pattern (`proposalCard` / `mrEntries` / filter chips):
- Brief title in the dated stream → expand → summary + the agent's **topics** and **target
  projects** shown for confirmation + source link → **Approve / Reject / Comment** (the same
  controls the review queue uses today; comment stored with the decision).
- A **Research** filter chip is added alongside the existing All / Awaiting me / Decided, so Phil can
  isolate or hide research in one click and it never crowds out the self-improvement items.

**Auto-admit — OPEN DECISION (not finally settled).** Phil raised "some findings auto-admitted by the
agent." Recommended default: **start all-review** (every finding awaits Phil's one-click approve;
nothing lands unseen), and flip on confidence-gated auto-admit later once the routing has earned
trust — matching the "prove it first" and "don't pollute" discipline used throughout. Auto-admit is
the one thing that would bypass the approval gate that authorises vault writes (§4), which is exactly
why it's held back. **Needs Phil's final call before the review task is built.**

### 4. Approval → a curated vault write (the governance reconciliation)

The durable home for an approved finding is the **Open Memory Vault**, not a centre's file folder.
This intersects Phil's most-protected rule, so it is handled deliberately:

> The vault contract: durable writes happen **only via `/capture`**, interactively, with Phil's
> explicit approval — never silently, in the background, or automated. "A missed capture is
> recoverable; a polluted vault is not."

**Reconciliation:** Phil's **Morning Report approval *is* the explicit sign-off** the vault requires.
Nothing durable is written until he approves. On approval:
- raw source → `vault/raw/` (immutable), following the `og-res-video-yt` precedent (which already
  writes video research as raw transcript + a marked `wiki/` block);
- the distilled insight → the right `vault/wiki/<topic>.md` page (or a `projects/` page when
  project-specific), tagged with the agent's topics, cross-linked so it surfaces on those topics later;
- `index.md` updated and a `logs/vault-log.md` breadcrumb appended, per the contract.

Rejected or unreviewed findings never touch the vault. This honours the *spirit* of `/capture`
(explicit approval, curated, not silent) while letting approval flow into the vault.

**OPEN — vault-writer mechanism.** The exact writer needs settling against the vault `AGENTS.md`
before the vault task is built: the dashboard approve-click is not itself a Claude `/capture`
session, and the contract says "one writer at v1: Claude / no out-of-band writes." Options to resolve:
(a) approval stages a capture draft a Claude/nightly step commits under the contract; (b) an
approval-triggered writer that follows the contract rules directly, documented as a Phil-authorised
extension of the `/capture`-only rule for approved research findings. Re-read the vault `AGENTS.md`
and confirm the mechanism before implementing.

### 5. Skill-centre Intelligence = a tag-query over the vault

The vault is the **single source of truth**; each centre's Intelligence section becomes a **live,
parameterised query into it**, not a folder of copied files:

- SEO's Intelligence runs "give me everything tagged `seo`" and renders the matches live. A finding
  tagged `seo`+`design` shows in both centres automatically, with **zero duplication**. Retag it and
  both views update themselves.
- Scope: surfaces **everything in the vault tagged with the centre's topic — not just research
  findings** but any captured insight, video note, etc. That makes each Intelligence section the
  domain's whole discoverable memory. Each entry carries a small **source marker** (research /
  capture / video) so Phil knows where it came from.
- Reading the vault with a query touches none of the `/capture` write-gate — the dashboard already
  reads the vault this way (e.g. the garden buy-list), so this stays local-first and rule-safe.
- Implementation: a read-only endpoint (e.g. `GET /api/vault-intelligence?tag=<code>`) scanning
  vault frontmatter tags; add a small tag-index only if the scan gets slow as the vault grows.

### 6. Humanizer split, and the social broadcast surface

- **Humanizer.** Internal findings (Morning Report, vault) get a **plain, tight, scannable** writing
  standard in the curation prompt — humanising for readability. The **`meta-humanizer` skill** runs
  where it's designed to and where Phil's standing rule mandates it: on **external** copy — i.e. the
  social drafts — as a finishing pass before Phil ever sees them.
- **Social.** The dashboard's **Social tab + `social_queue`** already exist: they harvest `##
  Postables` from digests and hand Phil per-platform copy (X thread-split, LinkedIn, copy/deep-links)
  with approve/skip/mark-posted. The missing piece was *identification*, now handled by the social
  lens in §2: a social-worthy finding's draft (meta-humanised) flows straight into that existing
  queue. No new daemon; the "Social Media Agent" is a named step in the pipeline.

## The surfaces, and what each is for

- **Morning Report** — *decide*. The one in-tray; transient; filterable so research never buries the
  self-improvement items.
- **Skill-centre Intelligence** — *what this domain knows*. A tag-query view over the vault,
  discoverable in context.
- **Memory Vault** — *the durable source of truth*, discoverable everywhere via tags.
- **Social queue** — *broadcast*. Fed by the social lens; meta-humanised; Phil one-taps to post.

One approval fans a finding into the vault (which the centre views query) and, if social-worthy, into
the Social queue — from a single checkpoint.

## What this changes from the Phase-1 build

- **AI routing re-enabled + multi-target.** Phase-1 Task 12 made the feed's configured target drive
  routing and forbade the LLM from guessing; v2 has the agent assign topic(s) + target centre(s),
  possibly several. The feed-level target and the per-finding reassign control become **manual
  overrides** for when the agent gets it wrong — not the primary path.
- **Review moves to the Morning Report.** The portal's Inbound section and per-centre "Pending
  review" lists are superseded by the Morning Report checkpoint. The Feeds/sources screen stays.
- **Vault replaces centre-repo digest copies.** Phase-1 pooled all digests into Research and wrote an
  `accepted/` file into the target centre repo (which the portal didn't even render). v2 writes once
  to the vault on approval; centre Intelligence queries it by tag. No per-centre duplication.
- **The tags/target multi-select** (just built) becomes the override UI, not a required step.

## Open decisions (resolve before building the affected tasks)

1. **Auto-admit policy** (§3) — default all-review to start; Phil's final call needed.
2. **Vault-writer mechanism** (§4) — settle against vault `AGENTS.md`; the approval-triggered write
   must stay contract-compliant.

## Out of scope (for this revision)

- NotebookLM as a source (still a manual, Phil-triggered future track).
- A Hermes cron to automate the sweep (still run by hand until the loop is proven — unchanged from
  Phase 1's rollout discipline).
- Changing the sources/Feeds UI (it stays as built).
