# ADR-019: CRM System-of-Engagement Ownership Path

- **Status:** Proposed
- **Date:** 2026-10-20
- **Decision Owners:** CEO LOO (accountable); Integrations + Architect (review)
- **Related:** [ADR-003](ADR-003-execution-gateway-into-runtime.md),
  [ADR-014](ADR-014-outcome-based-systems-engineering.md),
  [ADR-016](ADR-016-sovereign-intelligence-doctrine.md),
  [ADR-018](ADR-018-restricted-external-gateway.md),
  [../offers/revenue-capture-appointment-os/ghl-capabilities.md](../offers/revenue-capture-appointment-os/ghl-capabilities.md),
  [../roadmap/ghl-readonly-governed-write.md](../roadmap/ghl-readonly-governed-write.md)

## Context

AION already treats GoHighLevel (GHL / LeadConnector) as the **CRM
system-of-engagement (SoE)** behind Runtime: contacts, opportunities, pipelines,
notes, tasks, appointments, and message drafts are reached through catalogued
`crm.*` capabilities and the GHL adapter (Mission 009 / AIO-17). AION Data owns
governance truth (executions, approvals, side effects, outcomes, audit). ADR-016
and ADR-018 classify GHL as **optional-external / SoE-authorized** — never the
intelligence system of record — and require gateway policy, not an untested cut.

The open question is whether AION should **replace GHL** with a fully owned CRM
so customization and connectivity are easier. Three realistic paths exist:

| Path | What it is | Ownership | Channels / UI | Fit to current `crm.*` |
|---|---|---|---|---|
| **A. GHL-keep** | Keep GHL as SoE; harden REG + adapter | Vendor SoE; AION owns governance | SMS/email, appointments, agency UI today | Already live |
| **B. Twenty-adapter** | Self-host a mature OSS CRM (Twenty-class); new Runtime backend | Self-hosted SoE; AION owns host + schema evolution within that product | Depends on product; weaker SMS/agency surface than GHL today | New `CrmBackend` implementing same `crm.*` |
| **C. Thin AION CRM** | Build only the entities `crm.*` models on an AION-owned store + UI | Full product ownership | AION must build messaging, calendar, multi-tenant UI | Native; no vendor mapping |

A fourth idea — adopting [kargulstudio/sales-crm](https://github.com/kargulstudio/sales-crm)
— is **not** a CRM path. That repository is a Next.js / Tailwind **boilerplate**
(“Kargul Starter”), not contacts/pipelines/appointments/messaging. It is
rejected as a substitute for GHL.

Forces:

1. **Sovereignty** (ADR-016/018): authorized SoE egress is allowed; CRM must not
   become intelligence SoR; recovery path must exist if GHL is shut off.
2. **Revenue path:** Flagship is Revenue Capture & Appointment OS; GE-001 and
   live RCAOS certification still lead over platform expansion.
3. **Capability contract:** Runtime already exposes provider-shaped `crm.*`
   serviceKeys with fake/live backends — the correct seam for any future swap.
4. **Channel reality:** GHL’s value is not only CRUD CRM — it is client-facing
   CRM UI plus messaging/booking surface that agents and humans share.
5. **Capability-over-vendor:** Do not lock “Twenty” (or any OSS CRM) until a
   replacement is actually triggered and scored against the `crm.*` matrix.

## Decision

We will adopt a **staged CRM SoE ownership path**:

1. **Near term (in force with this ADR once Accepted): Path A — GHL-keep.**
   GHL remains the authorized CRM SoE. Priorities are Restricted External
   Gateway enforcement around GHL (ADR-018 / SOV-003), production write
   eligibility, and live RCAOS / GE-001 evidence — not a CRM rewrite.

2. **Standing seam rule.** All CRM mutations continue to go through Runtime
   catalogued `crm.*` capabilities and a backend interface (today:
   `GhlBackend` / fake|live). No product or agent may treat GHL (or any future
   CRM) as intelligence SoR. Side-effect ledger + idempotency remain mandatory.

3. **Ownership trigger (when Path B or C may be opened).** Start a replacement
   evaluation only when **one or more** of these are true and recorded:
   - GHL customization / API limits block a sold client outcome that `crm.*`
     already models;
   - a tenant class requires **self-hosted CRM** under sovereignty or contract;
   - GHL commercial or availability risk makes a recovery drill fail;
   - AION deliberately shrinks the offer to entities/channels it will own.

4. **If triggered: prefer Path B before Path C.** First candidate class is a
   mature self-hosted OSS CRM with a stable API (Twenty is the **reference
   candidate**, not a locked vendor). Implement a second Runtime CRM backend
   that satisfies the existing `crm.*` matrix (fake proofs first; dual-run;
   then migrate customer-zero). Choose Path C (thin AION CRM) only if the
   required surface is small enough that owning UI + messaging + calendar is
   cheaper than adapting an OSS CRM — recorded in a follow-up ADR.

5. **Explicit non-decisions.** This ADR does **not** authorize building a
   greenfield CRM UI, forking marketing boilerplates, or migrating live client
   GHL locations. It does **not** remove GHL from the ADR-018 SoE allowlist.

## Alternatives Considered

- **Path A — GHL-keep (chosen for near term).** Keep GHL as SoE; invest in
  gateway policy, adapter fidelity, and outcome proof. Accepted near term
  because the revenue and sovereignty programs already assume authorized SoE
  egress; rip-and-replace would displace GE-001 / RCAOS without buying Core
  sovereignty (governance SoR is already AION’s).

- **Path B — Twenty-adapter (deferred).** Self-host Twenty (or EspoCRM /
  SuiteCRM-class) and map `crm.*` onto its API. Deferred, not rejected: best
  ownership path when the trigger fires, because it reuses a real CRM product
  and preserves the Runtime seam. Rejected *now* because it does not remove
  near-term P0 work, and channel/UI parity with GHL is unproven for
  Appointment OS.

- **Path C — Thin AION CRM (deferred, higher bar).** Own contacts /
  opportunities / notes / tasks / appointments in an AION store with a minimal
  operator UI. Deferred: maximum control, but AION inherits SMS/email,
  calendar, multi-location UX, and client CRM training — a product company
  inside the platform company. Rejected as the default replacement.

- **Adopt kargulstudio/sales-crm (or similar starters) as “the CRM”.**
  Rejected: not a CRM; would be Path C disguised as Path B with no data model,
  API, or channels.

- **Immediate hard cutover off GHL.** Rejected: violates ADR-016
  audit-then-migrate and risks live revenue paths (ADR-018 already rejected
  “block GHL until fully local CRM”).

## Consequences

### Positive

- Aligns CRM ownership talk with ADR-016/018: SoE allowed, SoR forbidden.
- Preserves the `crm.*` contract as the only swap surface — future adapters do
  not rewrite Core or offer process contracts.
- Avoids a false start on a boilerplate “CRM” that cannot run Appointment OS.
- Gives a clear trigger for when ownership engineering is justified.

### Negative

- GHL API / UX limits remain until a trigger fires.
- PII still egresses to GHL under `soe_authorized` (accepted under REG).
- Path B/C still require later license, ops, and migration ADRs.
- Dual-backend period (if Path B starts) increases proof and config surface.

## Implementation Notes

- **Docs / doctrine:** Keep GHL language as SoE in sovereignty inventory and
  REG allowlist; do not invent a parallel “AION CRM Core” without a trigger.
- **Runtime:** Prefer extracting a provider-neutral CRM backend port from
  today’s GHL types when a second backend is approved — not before. Fake
  backend and `proof:*` matrix remain the acceptance spine.
- **Offers:** Appointment OS capability list
  ([ghl-capabilities.md](../offers/revenue-capture-appointment-os/ghl-capabilities.md))
  is the scorecard for any Path B/C candidate (including messaging and
  appointment create — still deferred on GHL).
- **Out of scope for this ADR:** picking Twenty vs Espo vs Suite; schema
  design for Path C; client data migration runbooks.

## Follow-up Decisions

- Accept or amend this ADR after leadership review (status → Accepted).
- Complete SOV-003 Runtime enforce around GHL `fetch` (ADR-018) — prerequisite
  hygiene for any SoE, including a future self-hosted CRM.
- When an ownership trigger fires: open **ADR-0xx CRM SoE provider selection**
  (score Path B candidates against `crm.*` + channels) or **ADR-0xx Thin AION
  CRM scope** if Path C is chosen.
- Optional engineering spike (only if scheduled against spare capacity):
  document `CrmBackend` port shape vs current `GhlBackend` — no production
  cutover.
