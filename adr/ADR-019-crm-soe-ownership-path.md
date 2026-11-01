# ADR-019: CRM System-of-Engagement Ownership Path

- **Status:** Accepted
- **Date:** 2026-10-20
- **Accepted:** 2026-10-20
- **Decision Owners:** CEO LOO (accountable); Integrations + Architect (review)
- **Mission:** [CRM-001](../.aion/missions/CRM-001-twenty-integration-benchmark.md)
- **Related:** [ADR-003](ADR-003-execution-gateway-into-runtime.md),
  [ADR-014](ADR-014-outcome-based-systems-engineering.md),
  [ADR-016](ADR-016-sovereign-intelligence-doctrine.md),
  [ADR-018](ADR-018-restricted-external-gateway.md),
  [../.aion/audits/crm-001-infrastructure-reuse-2026-10-20.md](../.aion/audits/crm-001-infrastructure-reuse-2026-10-20.md),
  [../offers/revenue-capture-appointment-os/ghl-capabilities.md](../offers/revenue-capture-appointment-os/ghl-capabilities.md)

## Context

AION’s live revenue path today uses **GoHighLevel** as the CRM
system-of-engagement behind Runtime (`crm.*` capabilities, GHL adapter, fake/live
backends, external side-effect ledger). ADR-016 / ADR-018 allow authorized SoE
egress and forbid treating any vendor CRM as intelligence system of record.

Leadership established **AION CRM — Sovereign Infrastructure V1**
(2026-10-20): replace GHL as the *primary* operational CRM with a self-hosted,
API-extensible product, while keeping AION’s control plane as the durable core.
The objective is not only to leave GHL — it is to make CRM a **native,
commercially deployable component of AION OS**.

Target roles:

| Component | Decision | Role |
|---|---|---|
| Primary CRM | **Twenty CRM** | API-extensible operational interface (SoE) |
| Benchmark | **EspoCRM** | Lightweight performance / deploy comparison |
| Source of truth | **AION Data** | Canonical customer and revenue records |
| Intelligence | **AION AI Layer** | Locally controlled models and agents |
| Execution | **AION Runtime** | Governed actions, permissions, audit |
| Workspace | **AION OS** | Unified operator and client experience |

Forces:

1. **Reuse first.** Runtime already has a provider-shaped CRM plane; inventing a
   parallel CRM microservice would duplicate governance.
2. **Vendor independence.** Twenty must be swappable without rebuilding Core.
3. **Data boundary tension.** `aion-data` today excludes CRM entity tables and
   documents “GHL owns CRM state.” V1 asserts AION Data as customer SoT — that
   requires a **follow-up data-layer ADR** before entity migrations. Until then,
   governance + outcomes + external id maps live in aion-data; Twenty holds the
   operational working set.
4. **Staged migration.** Production GHL tenants must not be hard-cut; dual-run
   and rollback are mandatory.
5. **Non-CRM starters** (e.g. marketing boilerplates named “sales-crm”) are not
   candidates.

## Decision

We will adopt **AION CRM Sovereign Infrastructure V1** as follows:

1. **Primary SoE: Twenty CRM (self-hosted).** Twenty is the operational CRM
   interface for AION OS. It is deployed under AION control (private networking,
   isolated credentials, database separate from `aion_data`).

2. **Benchmark: EspoCRM.** EspoCRM is used for memory/CPU/API/deploy comparison
   during CRM-001. It is **not** the production SoE unless a later ADR promotes
   it.

3. **Execution: AION Runtime only.** Agents and products never access Twenty (or
   Espo, or GHL) databases or raw APIs. All CRM mutations go through catalogued
   `crm.*` capabilities and a provider backend port (today `GhlBackend` → extract
   to `CrmBackend`). Approvals, risk tiers, idempotency, and the external
   side-effect ledger remain mandatory.

4. **Source of truth: AION Data (canonical customer & revenue), with a gated
   schema path.** Governance truth already lives in aion-data. Canonical
   customer/revenue **entity** storage in aion-data is **authorized as intent**
   but **blocked on implementation** until a follow-up ADR amends the “no CRM
   tables” rule and defines sync/projection vs ownership. CRM-001 must not add
   those tables preemptively.

5. **GHL becomes a migration adapter.** GHL remains on the REG SoE allowlist for
   staged reconciliation, client isolation tests, and rollback. It is no longer
   the strategic primary CRM.

6. **First milestone: CRM-001 — Twenty Integration & Benchmark.** Sequence:
   inspect/reuse audit → deploy Twenty (dev) → benchmark EspoCRM → AION CRM
   adapter → agents via Runtime → first real workflow proof → staged GHL
   migration plan. **Create no additional microservices** until reuse is
   exhausted (see audit).

7. **Three non-negotiables** bind this ADR: sovereign ownership; vendor
   independence (swap Twenty without rebuilding Core); offline resilience for
   core records, local intelligence, and internal workflows.

## Alternatives Considered

- **Path A — GHL-keep as permanent primary SoE.** Rejected as the strategic end
  state: limits customization and keeps primary customer operations on a rented
  SaaS SoE. GHL is retained only as migration/rollback adapter.

- **Path B — Twenty-adapter (chosen as primary SoE).** Self-host Twenty; map
  `crm.*` through Runtime. Accepted: API-extensible, self-hostable, aligns with
  TypeScript stack and vendor-independence via the existing capability seam.

- **Path C — Thin AION CRM (deferred).** Build only `crm.*` entities + UI in
  AION. Deferred: maximum ownership but forces AION to own messaging, calendar,
  and CRM UX immediately. May revisit after Twenty is proven if the operational
  surface shrinks.

- **EspoCRM as primary SoE.** Rejected for V1 primary (used as benchmark only):
  leadership selected Twenty for the operational interface; Espo informs
  performance/deploy tradeoffs.

- **New aion-crm microservice / greenfield repo.** Rejected for CRM-001: the
  Runtime adapter plane + ledger already provide the governed boundary.

- **Marketing boilerplate “CRM” starters.** Rejected: not CRMs.

## Consequences

### Positive

- Clear primary SoE (Twenty) with a measurable benchmark (Espo).
- Preserves `crm.*` / Runtime as the swap surface — Core stays vendor-neutral.
- Aligns CRM work with sovereignty (self-hosted SoE, REG, offline intent).
- Reuse-first CRM-001 avoids parallel platforms.

### Negative

- Channel parity with GHL (SMS/email/agency UX) is not automatic in Twenty —
  later milestones.
- Dual-provider period increases proof and ops surface.
- Customer SoT in aion-data needs a follow-up ADR before schema work.
- Migration cost and client communication for GHL tenants.

## Implementation Notes

- **Reuse audit (blocking read before new services):**
  [../.aion/audits/crm-001-infrastructure-reuse-2026-10-20.md](../.aion/audits/crm-001-infrastructure-reuse-2026-10-20.md)
- **Runtime:** Extract provider-neutral CRM backend from
  `aion-runtime/src/adapters/ghl/`; add Twenty fake/live; keep GHL adapter.
- **Infra:** Compose overlay for Twenty (+ ephemeral Espo) on `internal`
  network; Traefik only if a controlled UI route is required; never share
  `aion_data` volume/DB with Twenty.
- **REG:** Prefer same-host/internal Twenty (not public SaaS egress); still
  classify CRM payloads; keep `ghl` allowlisted during migration.
- **Offers:** Generalize the Appointment OS capability scorecard from
  GHL-specific naming as adapters land.
- **Proofs:** Mirror `proof:rcaos-at` / AIO-17 matrices against fake then live
  Twenty before any GHL tenant migration.

## Follow-up Decisions

- **ADR (data-layer):** Canonical customer/revenue entities in aion-data —
  amend “no CRM tables” / sync vs own.
  Proposed as [ADR-020](ADR-020-canonical-crm-domain.md).
- **ADR (optional):** Promote or reject Espo after CRM-001 benchmark.
- Complete SOV-003 enforce path so new SoE providers cannot bypass GATEWAY.
- Workspace embedding of Twenty UI into AION OS (product-module or shortcut) —
  after CRM-001 workflow proof.
