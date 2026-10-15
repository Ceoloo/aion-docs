# AION Build Map — Creative OS Handoff

- **Date:** 2026-10-15
- **Owner:** CEO LOO
- **Status:** Working map (docs gate open)
- **Canon proposals:** [ADR-015](../adr/ADR-015-workspace-product-module-contract.md)
  (Proposed) · [MISSION-CREATIVE-001](../missions/MISSION-CREATIVE-001-creative-os-client-shorts.md)
  (Defined, proposed) ·
  [Product Module Contract](../architecture/product-module-contract.md)
- **Working mirror:** [Notion — AION Build Map](https://www.notion.so/3fa5b5fa4f8f812aa946fa82536bf9b0)

AION is one governed operating system — the Workspace — with product modules
plugged into it through Runtime; Creative OS is the first module, and AION's
own recordings then one services client are its first users.

## At a glance

People work in the Workspace; Runtime is the only path to anything that changes
state; AION Data keeps the governance record while each module keeps its own
product data.

```
Browser ─▶ Workspace (BFF) ─▶ Runtime ─(module adapter)─▶ Module server API ─▶ Module workers
   │                                    │
   └─ module window (same parent domain, SSO handoff) ─▶ Module web app
                                        └─▶ AION Data (governance truth)
```

## Where we're heading

We are building one business operating system, not a pile of separate apps. A
business works inside the **AION Workspace**; every action that changes
something goes through **AION Runtime**, which checks identity, asks a human
when needed, and records what happened. Products such as **Creative OS** plug
in as modules and do the specialist work.

Creative OS turns a talking-head recording into approved vertical shorts. AION
uses it on its own recordings first, then delivers it to one client of AION's
services, whose own approver signs off each short. Later modules (Wealth OS,
Music Label OS and others) plug in the same way.

The ideas from the older repos (CreatorOS apps, content-brain, the multi-agent
experiments) are kept as sources to port from, not as parallel products.

## Repositories

Nine repos are active in this build; four older repos are idea sources and two
empty ones are being archived.

| Repo | Role | Owner area | Status | Next step |
|---|---|---|---|---|
| [aion-command-center](https://github.com/Ceoloo/aion-command-center) | AION Workspace: desktop shell, Assistant, Missions, Approvals | Frontend | OS-001–003 built | GE-001, then OS-004 (app registry depth) |
| [aion-runtime](https://github.com/Ceoloo/aion-runtime) | The one gateway: identity, catalog, gates, executions, adapters | Runtime | In production | Later: `creative` module adapter |
| [aion-data](https://github.com/Ceoloo/aion-data) | Governance records in Postgres (missions, approvals, outcomes) | Data | Migrations 0001–0014 | No change expected |
| [aion-core](https://github.com/Ceoloo/aion-core) | Kernel contracts and policy | Runtime / AI | Pinned by Data and Runtime | No change expected |
| [aion-docs](https://github.com/Ceoloo/aion-docs) | ADRs, contracts, missions, agent team rules | Architect / Docs | ADR-015 + mission proposed | Accept or revise ADR-015 / MISSION-CREATIVE-001 |
| [aion-infra](https://github.com/Ceoloo/aion-infra) | Deploy profiles, domains, secrets | Infra | — | Parent domain and subdomains when purchased |
| [aion-creative-os-CVD](https://github.com/Ceoloo/aion-creative-os-CVD) | Creative OS module: media, clips, edit plans, renders | Product | Phase 1 (media intake) done | Phase 2: transcription |
| [content-brain](https://github.com/Ceoloo/content-brain) | Own-post capture, scoring, Postiz drafts; separate competitor bucket | Product | Built, never run | Fill config and secrets, first run |
| [IclipU-by-Aion](https://github.com/Ceoloo/IclipU-by-Aion) | Future consumer brand for the clipper | Product | Brand only | Waits for customer identity |
| CreatorOS-CCM, CreatorOS-ViDHub, Creator-OS-Webapp, Multi-Agent-Content-Team | Idea sources: Brand Memory, ContentPack, UI views, transcriber | — | Reference only | Port pieces when a phase needs them |
| Content-Machine-Factory, Multi-Modal-Creative-Director-Platform | Empty placeholders | — | Archive PRs open | Archive after merge |

## Decisions already made

| Decision | Choice | Recorded in |
|---|---|---|
| How modules join the Workspace | One Product Module Contract, through Runtime | ADR-015 (Proposed) |
| Who decides and audits | Runtime only; modules execute | ADR-002, ADR-005, ADR-015 |
| Where data lives | Product data in the module's own store (Creative OS: Supabase); governance in AION Data | ADR-015 (amends earlier "all canonical entities in aion-data" for product-module satellites) |
| Creative OS repo status | Satellite outside the six platform repos | ADR-015 |
| How Creative OS opens | Its own web app in a Workspace window, same parent domain, SSO handoff | ADR-015 |
| Who approves client work | The client, with their own Runtime approver login | ADR-015, MISSION-CREATIVE-001 |
| First external customer | One client of AION's services (done for you) | MISSION-CREATIVE-001 |
| Own posts vs competitors | Separate buckets; competitors only as labelled signals | content-brain PR #7, CVD `docs/ecosystem.md` |
| Consumer brand | IclipU, after customer identity exists | IclipU PR #1 |
| Editing tools | Workflow layer that exports to existing editors, not our own editor | Discussion only; needs a doc |
| Open-source Craft apps | Use as engines, don't fork yet | Discussion only; needs a doc |

## How one request flows

Example: an operator asks for three shorts from a client's recording.

1. The operator asks in the Workspace Assistant, or opens Creative OS in its
   window and uploads the recording.
2. The Workspace server submits `creative.clip.propose@1` to Runtime for the
   client's tenant.
3. Runtime checks the operator's login and permissions, then calls Creative OS
   through its module adapter.
4. Creative OS transcribes the recording and proposes three clips taken from
   real transcript timestamps.
5. The operator picks a clip and finalises the edit plan. Creative OS saves it
   as a revision with a hash.
6. The operator submits `creative.short.render@1`. Runtime opens a human gate
   showing the revision, hash and a preview.
7. The client approves in the Workspace Approvals app with their own login.
8. Runtime resumes the run. Creative OS renders only if the approval and its
   own revision hash both match.
9. The MP4 appears as a mission artifact behind a short-lived link. Runtime
   records the side effect and the outcome.
10. The client publishes the short themselves. Nothing is posted automatically.

## Rules every team follows

- **One authority.** Runtime decides, approves and audits. Modules execute. No
  module runs its own approval loop.
- **One inbox.** Approvals are decided in the Workspace Approvals app, never
  inside a module.
- **Identity comes from the server.** The browser never chooses its tenant,
  user or role. Module SSO claims come from a Runtime-authenticated principal.
- **Data stays in its lane.** No product tables in AION Data; no governance
  records in module stores.
- **AI outputs data, code executes it.** No AI-written shell commands, FFmpeg
  arguments or filter graphs.
- **Approval is tied to an exact revision.** Any edit after approval needs a
  new approval.
- **Own posts and competitor posts stay apart.** Competitor data is only a
  labelled signal, never copied into drafts.
- **Drafts only.** Nothing is published or scheduled automatically.
- **No fake numbers.** No made-up metrics, retention predictions or success
  claims without real evidence.
- **Planned until proven.** A module shows as planned in the Workspace until
  every advertised gated service has acceptance evidence.
- **Secrets stay server-side.** No privileged keys in browser bundles or
  committed files.
- **Idempotency crosses the adapter.** The stable key travels to the module;
  retries return the original result, not a second artifact.

## Build order and gates

Current company priority stays first: GE-001 certification and the
[Pilot-to-Revenue Program](../offers/aion-pilot-to-revenue-program.md). Creative
OS work runs alongside without taking their capacity.

1. **Now, docs:** accept or revise ADR-015 and MISSION-CREATIVE-001. Turn on
   content-brain for AION's own posts so real baselines start building.
2. **Creative OS Phases 2–5 (Stage A, customer zero):** transcription, three
   clip proposals, approved edit plans, rendering. Gate: a real AION recording
   becomes an approved, rendered short.
3. **Workspace OS-004:** app registry depth, with Revenue Copilot as its first
   module. Creative OS stays listed as planned.
4. **Creative OS Stage B, one client:** tenant scoping, Runtime adapter (fake
   backend first), the two creative services, the client's approver login, SSO
   window once the domain exists. Gate: every row of the contract's
   acceptance-evidence table passes on the client's tenant, and GE-001 is
   certified.
5. **Validation:** compare operator time per approved short with the measured
   baseline; record whether the client keeps paying. Decide continue, iterate
   or kill.
6. **Then, one at a time:** Content Brain into Creative OS, campaign packs,
   ColorGrade engine, editing-tool export, IclipU consumer app.

## Idea backlog

Every idea raised so far, where it sits, and what has to happen before it is
built.

| Idea | Where it lives | Status | Gate before building |
|---|---|---|---|
| Clip Studio (recording to shorts) | Creative OS | Building (Phase 1 done) | — |
| Content Brain (own posts + competitor signals) | content-brain now; Creative OS later | Built, not yet run | Real baselines from AION's own posts |
| Campaign Studio (one idea to a multi-channel pack) | Creative OS, ported from CreatorOS-CCM | Planned | Creative OS Stage B validated |
| Brand Memory (one shared brand profile) | Shared schema, from CCM + content-brain voice files | Planned | Needed by Campaign Studio |
| ColorGrade OS (batch colour correction and matching) | Separate engine; desktop parts later | Discovery only | Engine bake-off and 75% accepted-shots test on real footage |
| Editing suite | Workflow layer exporting to Premiere, Resolve, FilmCraft | Idea | Test: export a Creative OS plan as OTIO/FCPXML and open it in FilmCraft |
| Craft apps (FilmCraft, PhotoCraft and others) | Used as engines over their command line and MCP | Parked except 3 tests | A specific feature needs one |
| IclipU consumer app | Same Creative OS services, self-serve | Brand only | Workspace customer identity; Stage B validated |
| Desktop tools in the Workspace | Native Companion (V2) | Not started | Native Companion program starts |

## Open decisions

- [ ] Accept or revise ADR-015 and MISSION-CREATIVE-001 (CEO LOO; Architect review)
- [ ] Name the client for Creative OS Stage B (CEO LOO)
- [ ] Confirm the proposed targets with that client: published shorts in 4 weeks, at least 50% less operator time (CEO LOO + client)
- [ ] Buy the parent domain for the Workspace and module subdomains (CEO LOO, then Infra)
- [ ] Choose how module status reaches Runtime: polling or callback (Runtime, during the first adapter)
- [ ] Choose the SSO assertion format and key storage (Runtime + Frontend + Security)
- [ ] Record `aion-command-center` in the repository maps as canon or satellite (Architect; relates to ADR-013)
- [ ] Write down the editing-suite and Craft-apps direction as docs once their tests run (Product)
- [ ] Merge the archive README PRs, then archive the two empty repos (CEO LOO)

## Sources

- [ADR-015](../adr/ADR-015-workspace-product-module-contract.md) and
  [MISSION-CREATIVE-001](../missions/MISSION-CREATIVE-001-creative-os-client-shorts.md)
- [Product Module Contract](../architecture/product-module-contract.md)
- [Creative OS ecosystem plan](https://github.com/Ceoloo/aion-creative-os-CVD/blob/main/docs/ecosystem.md)
- [Creative OS build plan](https://github.com/Ceoloo/aion-creative-os-CVD/blob/main/docs/build-plan.md)
- [Runtime operator loop evidence format](https://github.com/Ceoloo/aion-runtime/blob/main/docs/operator-loop-v1.md)
- [content-brain market bucket — PR #7](https://github.com/Ceoloo/content-brain/pull/7)
- [IclipU brand role — PR #1](https://github.com/Ceoloo/IclipU-by-Aion/pull/1)
- [Pilot-to-Revenue Program](../offers/aion-pilot-to-revenue-program.md)
