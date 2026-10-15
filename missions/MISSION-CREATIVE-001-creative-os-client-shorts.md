# Mission: Creative OS v1 — governed short-form clips for one AION services client

- **Mission ID:** MISSION-CREATIVE-001
- **Owner:** CEO LOO (decision) · Creative OS delivery (`aion-creative-os-CVD`)
- **Status:** Defined (proposed; pending CEO LOO approval)
- **Date:** 2026-10-15
- **Architecture:** [ADR-015](../adr/ADR-015-workspace-product-module-contract.md) (Proposed) ·
  [Product Module Contract](../architecture/product-module-contract.md) ·
  [repositories/aion-creative-os.md](../repositories/aion-creative-os.md)

## Problem

Founders and owners record talking-head video (updates, explanations, offers),
but turning a 5–20 minute recording into usable vertical shorts is slow manual
editing: finding the strong 30–60 second moments, cutting, framing and
captioning. AION has no governed way to do this for a client: no record of
which exact edit the client approved, no tie to missions and outcomes, and no
measured cost per usable short.

## Target User

1. **Customer zero:** AION operators editing CEO LOO's own recordings.
2. **First external user:** one client of AION's services (done-for-you),
   to be named by CEO LOO. AION operators run the work; the client's own
   approver signs off each short.

Not self-serve creators, agencies or editors (see Non-Goals).

## Expected Outcome

For the selected client, recordings become **client-approved vertical shorts**
that the client publishes themselves, with less operator editing time per
approved short than the measured manual baseline. Every render traces to a
mission, execution, client approval of the exact edit-plan revision/hash,
artifact and outcome.

## Scope

**Stage A — customer zero (Creative OS standalone).** Complete Creative OS
Phases 2–5 ([build plan](https://github.com/Ceoloo/aion-creative-os-CVD/blob/main/docs/build-plan.md)):
- timestamped transcription;
- three grounded clip proposals per recording;
- approved, hashed edit-plan revisions;
- deterministic 1080×1920 render with captions, preview and download.

Stage A is proven on AION's own recordings.

**Stage B — one client, governed through Runtime.**
- Tenant scoping in Creative OS (Runtime tenant id).
- Runtime module adapter (fake backend first), with catalog entries:
  - `creative.clip.propose@1`
  - `creative.short.render@1` (gated)
- The client's approver principal: a registered human actor with `approve`, scoped to the client tenant.
- Creative OS manifest in the Workspace (`planned` until evidence passes). It opens in a window via SSO once the parent domain exists, otherwise in a standalone tab.
- Evidence pack per contract section 10.

## Non-Goals

- Publishing, scheduling or posting anywhere (no Postiz, no platform APIs). The
  client publishes.
- Content Brain capture, competitor/market scanning, campaign packs.
- ColorGrade, editing-suite/FilmCraft handoff, Craft apps, desktop modules.
- IclipU or any self-serve signup, billing or pricing pages.
- More than one external client.
- Retention, view or ROI predictions; generative footage or B-roll; music
  licensing.
- Face tracking or automatic reframing beyond reviewed center crop.
- Displacing GE-001 or the [Pilot-to-Revenue Program](../offers/aion-pilot-to-revenue-program.md);
  this mission does not take their capacity.

## Success Metrics

Targets marked *proposed* are confirmed with the client at kickoff and recorded
in the validation gate before measurement starts.

| Metric | Type | Target |
|---|---|---|
| Client-approved shorts the client actually publishes (link evidence) | Outcome | *Proposed:* agreed count over 4 weeks |
| Operator time per approved short vs measured manual baseline | Outcome | *Proposed:* ≥ 50% less |
| Shorts approved on first review | Quality | Tracked; no target until baseline exists |
| Renders without a matching Runtime approval + revision hash | Safety | **0** |
| Cross-tenant reads/renders/approvals succeeding | Safety | **0** |
| Client decision to continue (paid add-on or retained in service scope) | Commercial | Recorded yes/no with reason |

Volume alone (clips proposed, renders run) is not success.

## Architecture Impact

| Repo | Impact |
|---|---|
| `aion-docs` | ADR-015, Product Module Contract, this mission |
| `aion-creative-os-CVD` | Phases 2–5; `tenant_id` scoping; Runtime approval-id binding; adapter API; SSO exchange; framing lock |
| `aion-runtime` | `creative` module adapter, catalog entries, proof matrix (Stage B) |
| `aion-data` | No new entities expected; existing executions, approvals, side effects, outcomes |
| `aion-command-center` | Creative OS manifest, SSO assertion issuer, window messages (after OS-004) |
| `aion-infra` | Parent domain, subdomains, TLS, signing keys (when the domain is purchased) |
| `aion-core` | None expected |

Dependency rules hold: Creative OS does not import `@aion/core` or `@aion/data`.

## Data Requirements

- **Creative OS store (product data):** source media, derivatives, transcripts
  with provider/model, clip proposals, edit-plan revisions and hashes, renders.
  All tenant-scoped.
- **AION Data via Runtime (governance):** missions, executions, approvals,
  external side effects (render with idempotency key), outcomes with evidence
  references.
- **Cohort labels:** `creative_v1_customer_zero` and `creative_v1_client`. Set
  `productionEconomic=false` for customer-zero runs.
- No canonical entity added to `aion-data`. Gaps found in Stage B go through an
  aion-data migration review, not a product workaround.

## Security Considerations

- Client approver is an authenticated Runtime principal; operators never
  approve on the client's behalf for client-gated renders.
- Tenant isolation proven both ways (Creative OS RLS and Runtime tenant checks).
- Uploads, transcripts and AI output are untrusted data; AI never produces
  shell commands, FFmpeg arguments or filter graphs.
- Rights: the client confirms in writing that they own the recordings and have
  consent from people on camera before any processing.
- Short-lived signed URLs only; no tokens, signed URLs, transcript text or PII in
  logs.
- Risk mapping: clip proposals are internal drafts (R1); the render is
  client-gated per ADR-015 regardless of default R-level policy.

## Release Gate

Per [release-template.md](release-template.md):

- **Stage A:** Creative OS Phase 2–5 exit criteria met on real AION recordings,
  with `npm run check` and media tests green and documented limitations.
- **Stage B:**
  - All rows of the contract's acceptance-evidence table recorded on the client tenant;
  - GE-001 certified;
  - client approver principal provisioned;
  - client rights confirmation on file.
- Creative OS stays `planned` in the Workspace until Stage B evidence passes.

## Validation Gate

Per [validation-template.md](validation-template.md):

- Baseline recorded before Stage B: operator time per manually edited short for
  the same client.
- After the agreed period: published approved shorts, time per short vs
  baseline, first-pass approval rate, every rejected or failed short explained.
- Client feedback and the continue/pay decision recorded.
- Mission decision: continue (second client or more services), iterate
  (editing quality, workflow), or kill.

## Learning Requirements

- Why clips are rejected (wrong moment, meaning lost, framing, captions).
- Which edits operators still make by hand, which shapes the next renderer
  features.
- Caption alignment accuracy against the transcript.
- Time and provider cost per approved short.
- Whether done-for-you creative work is something clients will pay for, which
  informs IclipU and later modules.

## Stage linkage

| Stage | Mission stage | Depends on |
|---|---|---|
| A — customer zero | Build | Creative OS Phases 2–5 |
| B — one client | Build → Release | Stage A; ADR-015 accepted; GE-001; Workspace OS-004; domain or standalone-tab fallback |
| Validation | Validated / decision | Agreed measurement period with the client |
