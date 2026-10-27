# ADR-020: AION Canonical CRM Domain (customer and revenue system of record)

- **Status:** Proposed
- **Date:** 2026-10-27
- **Decision Owners:** CEO LOO (accountable); Integrations + Architect (review)
- **Amends:** [ADR-019](ADR-019-crm-soe-ownership-path.md) (resolves its data-layer follow-up)
- **Related:** [ADR-003](ADR-003-execution-gateway-into-runtime.md),
  [ADR-005](ADR-005-runtime-identity-plane.md),
  [ADR-016](ADR-016-sovereign-intelligence-doctrine.md),
  [ADR-018](ADR-018-restricted-external-gateway.md),
  [CRM-001 reuse audit](../.aion/audits/crm-001-infrastructure-reuse-2026-10-20.md),
  [CRM-001 cutover sequence](../roadmap/crm-001-cutover-sequence.md),
  [GE-001 certify checklist](../roadmap/governed-execution-certify.md)

## Context

ADR-019 made Twenty the primary CRM system of engagement (SoE), kept GHL as a
migration and rollback adapter, and stated that AION Data should become the
source of truth for canonical customer and revenue records. It left that last
part blocked: "Customer SoT in aion-data needs a follow-up ADR before schema
work." This is that ADR.

Facts as of 2026-10-27:

1. **AION Data excludes CRM records today.** `aion-data/README.md` lists
   "product/business entity schemas — no CRM, sales, content, or portal
   tables" under what Data does not own. `aion-data/docs/schema.md` says of
   `external_side_effects`: "GHL owns CRM state; AION owns governance truth."
   The reuse audit calls this the blocker between ADR-019 and the V1 goal.
2. **The only link between AION and a CRM record is the side-effect ledger.**
   `external_side_effects.external_resource_id` holds the provider id of each
   governed write. There is no AION-owned customer id, so the same customer in
   GHL, Twenty and a web form are three unrelated ids.
3. **The capability seam already exists.** Runtime exposes `crm.contact.*`,
   `crm.opportunity.*`, `crm.note.create`, `crm.task.create`,
   `crm.appointment.*`, `crm.message.*` and `crm.conversation.*` through a
   provider `CrmBackend` (GHL fake/live, Twenty fake/live), with risk tiers in
   `aion-runtime/src/control-plane.ts` (for example `crm.note.create` R1,
   `crm.opportunity.update` R2, `crm.conversation.send` R3).
4. **Data-quality guards are bound to providers.** The OL-001 incident
   (2026-09-27) wrote synthetic evidence onto a real GHL contact because
   `crm.note.create` is R1 and nothing classified the payload. The fix
   (`SYNTHETIC_TO_PRODUCTION_DENIED`, Runtime #57) only ran for `ghl-live` and
   had to be extended separately to `twenty-live` (AIO-109). Each new provider
   repeats that risk.
5. **Provider schemas move under us.** The Twenty overlay defaults to
   `twentycrm/twenty:v0.40.0` and the Gate C adapter invariants are written
   against v0.40. Docker Hub lists `v2.51.0` (2026-10-23) as latest. An agent
   model coupled to a provider's object model inherits that drift.
6. **Tenant row-level security in Data is still a scaffold.** Migration
   `0010_tenant_rls` keys policies on `aion.tenant_id` and fails closed when
   it is unset. An isolated probe (aion-docs PR #99) showed the previous
   Runtime image cannot read tenant-scoped rows under those policies, and
   production RLS has not been verified with the application role (AIO-35).
7. **Cash flow comes first.** GE-001 (first certified revenue workflow) is
   re-scoped to run on Twenty after CRM-001 Gate D (AIO-37, AIO-109). Nothing
   in this ADR may add to that critical path.

## Decision

We will make AION Data the system of record for a defined set of customer and
revenue entities, owned and evolved by AION, with CRM products as replaceable
projections. Concretely:

1. **Amend the AION Data boundary.** AION Data may own the canonical CRM
   entities listed below, and only those. The rule "Data persists truth; it
   never decides" is unchanged: no business logic in SQL, triggers or stored
   procedures. Product-specific shapes still do not belong in Data.

2. **Canonical entities (target model).** Each row is tenant-scoped and
   carries an AION-minted id that never changes when providers change.

   | Entity | Owns |
   |---|---|
   | `crm_customers` | Commercial account identity, lifecycle stage, tenant ownership |
   | `crm_contacts` | People, communication points, roles |
   | `crm_companies` | Organisations |
   | `crm_relationships` | Typed links between customers, contacts, companies and opportunities |
   | `crm_consents` | Per-contact, per-channel consent with source and timestamp |
   | `crm_leads` | Inbound source, attribution, qualification label and evidence reference |
   | `crm_opportunities` | Pipeline stage, value, owner |
   | `crm_activities` | Calls, meetings, notes, tasks (body or reference) |
   | `crm_external_identity_map` | AION id ↔ provider object id per provider and workspace |
   | `crm_events` | Append-only journal of every canonical change and every inbound provider change |

   Delivery is staged (see Implementation Notes). Stage 1 is the identity spine
   only: customers, contacts, companies, relationships, consents, the identity
   map and the event journal.

3. **AION owns ids.** Ids are opaque, prefixed and AION-minted (for example
   `cust_…`, `cont_…`, `comp_…`, `opp_…`). Provider ids live only in
   `crm_external_identity_map`, unique per `(tenant_id, provider,
   provider_workspace, provider_object, provider_id)`. Agents and products
   refer to customers by AION id. Payment processors stay authoritative for
   settlement; AION stores references and verified outcomes (existing
   `outcomes`), not card or bank data.

4. **Field authority is explicit, and sync starts one-way.** Every canonical
   field has exactly one authority, recorded in a field-ownership table that
   ships with the schema:
   - AION-authoritative: identity, tenant, lifecycle stage, consent,
     dedupe/merge state, qualification label, and any field changed through a
     governed Runtime action.
   - Provider-authoritative: fields a human edits in the provider UI that AION
     does not govern (named per provider in the ownership table).

   Stage 1 to Stage 3 sync is **outbound projection**: canonical changes are
   pushed to providers through the existing `CrmBackend` adapters and
   side-effect ledger. Inbound provider changes are received over an
   authenticated endpoint, recorded in `crm_events` with provenance, and
   applied to the canonical record only if the field is provider-authoritative
   or a governed reconcile action accepts them. There is no automatic
   last-write-wins. Full two-way conflict resolution needs its own ADR.

5. **All access goes through Runtime.** Canonical entities are read and
   written only through governed Runtime capabilities (ADR-003). Agents and
   products never receive SQL access, connection strings or provider API keys.
   Canonical operations extend the existing `crm.*` catalog rather than
   creating a parallel tool set. Proposed additions, with tiers to be fixed in
   the catalog PR:

   | Capability | Purpose | Proposed tier |
   |---|---|---|
   | `crm.customer.resolve` | Find a customer by identifiers; never creates | R1 |
   | `crm.customer.create` | Create after resolve finds no match | R2 |
   | `crm.customer.merge` | Merge duplicates, keeping both histories | R3 |
   | `crm.customer.context` | Authorised customer, relationship and consent view | R1 |
   | `crm.customer.timeline` | Ordered events, activities and outcomes | R1 |
   | `crm.lead.qualify` | Record label plus evidence reference | R2 |
   | `crm.opportunity.advance` | Stage transition | R2 |
   | `crm.activity.record` | Note, call or task on a customer | R1 with mandatory data-class check |
   | `crm.outreach.prepare` | Reviewable draft; never sends | R2 |

   Sending stays on `crm.message.send` / `crm.conversation.send` (R3).

6. **The evidence-class guard moves to the canonical write.** Every canonical
   row and event carries `data_class` (`production`, `synthetic`, `test`,
   `fixture`). The canonical write path refuses to mix classes on one customer
   and refuses to project non-`production` data to a live provider, returning
   `SYNTHETIC_TO_PRODUCTION_DENIED`. The per-provider guard in the CRM adapter
   stays as defence in depth. A new provider therefore inherits the boundary
   without code of its own.

7. **Tenant isolation and privacy are on from the first table.** Every
   canonical table has `tenant_id NOT NULL` and `FORCE ROW LEVEL SECURITY`
   keyed on `aion.tenant_id`, failing closed. Personal data has a documented
   retention period. Erasure is a governed action: it deletes personal fields,
   leaves a non-personal tombstone in `crm_events`, and projects the deletion
   to every mapped provider. Consent is checked before any outreach capability
   runs.

8. **No new service.** Schema and repositories live in `aion-data`, ports and
   entity types in `aion-core`, governed capabilities and projection in
   `aion-runtime`. No `aion-crm` microservice or repository (consistent with
   ADR-019 and the reuse audit).

9. **This does not block CRM-001 or GE-001.** CRM-001 Gates A–D and GE-001 on
   Twenty proceed as planned. GE-001 evidence keeps provider ids in the
   side-effect ledger. When Stage 1 lands, a governed backfill maps those ids
   into `crm_external_identity_map` without rewriting the ledger.

10. **Production enablement has preconditions.** Canonical personal data is
    not written in production until (a) production RLS is verified with the
    application role and the AIO-35 previous-image finding is closed,
    (b) GE-001 has passed, and (c) the field-ownership table is reviewed.
    Non-production work can start once this ADR is accepted.

11. **Providers are pinned and benchmarked, and kept at arm's length.** Any
    provider projection targets a pinned provider version that has passed a
    compatibility benchmark. Twenty runs as a separate process reached only
    over its API. AION does not distribute a modified Twenty without a legal
    review of its licence terms.

## Alternatives Considered

- **Identity map only.** Keep CRM records in the provider; AION stores only
  the id map and the ledger (reuse-audit option 1). Rejected as the end state:
  AION cannot answer "who is this customer" without calling a provider, which
  fails the ADR-019 offline-resilience non-negotiable and leaves agent context
  coupled to provider schemas. Kept as the shape of Stage 1's identity map.
- **Twenty as permanent system of record** (ADR-019 as written). Rejected as
  the end state: AION's agent contracts would follow Twenty's object model and
  upgrade cadence (v0.40 → v2.51 drift already). Twenty remains the primary
  human workspace.
- **Full AION CRM application now** (ADR-019 Path C). Rejected for now: AION
  would have to build pipeline views, calendar, messaging and agency UX before
  any revenue proof. Views can be replaced one at a time later where AION
  workflows justify it.
- **New `aion-crm` microservice or repository.** Rejected: duplicates the
  governance plane (approvals, idempotency, ledger) that Runtime and Data
  already provide.
- **Generic JSON document store for customer data.** Rejected: no relational
  integrity, no reliable dedupe, and harder to apply row-level security and
  erasure.
- **Two-way sync with last-write-wins from day one.** Rejected: silently loses
  human or governed edits and the provenance of who changed what.

## Consequences

### Positive

- One AION customer id across GHL, Twenty, forms, calls and agents; provider
  changes stop breaking agent context.
- Data-quality and tenant boundaries are enforced once, at the canonical write,
  instead of once per provider.
- Customer context can be served when a provider is down or being replaced,
  meeting the ADR-019 offline-resilience requirement.
- Builds on existing seams (`crm.*`, `CrmBackend`, side-effect ledger,
  outcomes) rather than a second platform.

### Negative

- AION takes on schema, migration, privacy and security work for personal
  data, including erasure and retention.
- Projection and inbound journaling add moving parts; provider outages and
  duplicates must be handled and tested.
- Field-ownership rules will need revisiting as more provider fields are used.
- More data in aion-data raises the cost of the open RLS and rollback findings;
  they become hard preconditions.
- The Twenty UI can show data that lags the canonical record during
  projection.

## Implementation Notes

**Stages**

| Stage | Scope | Exit criteria |
|---|---|---|
| 0 | Accept this ADR; amend Data boundary docs | `aion-data` README and `docs/schema.md` updated; `repositories/aion-data.md` updated |
| 1 | Identity spine (non-prod): customers, contacts, companies, relationships, consents, identity map, events, `data_class`, RLS | Migrations with FORCE RLS; Core ports; Runtime `crm.customer.resolve/create/context`; cross-tenant denial and data-class tests; ledger backfill dry run |
| 2 | Projection to Twenty and GHL; inbound journal | Outbound projection via `CrmBackend`; authenticated inbound endpoint writing `crm_events`; duplicate and outage tests |
| 3 | Leads, opportunities, activities; remaining capabilities | Catalog entries with tiers; RCAOS AT-1…AT-7 re-run against canonical records |
| 4 | Production enablement | Decision §10 preconditions met; governed backfill of GE-001 ids |

**Per repository**

- `aion-data`: next free migration numbers on `main` (0014 is the latest);
  Postgres repositories; RLS policies modelled on `0010_tenant_rls` but with
  `FORCE ROW LEVEL SECURITY` from creation.
- `aion-core`: entity types, repository ports, capability catalog entries and
  risk tiers; the `data_class` rule as a pure function.
- `aion-runtime`: governed handlers for the new capabilities; projection that
  reuses `CrmBackend` and `external_side_effects`; inbound provider endpoint
  (authenticated, idempotent) writing to `crm_events`; move the
  `SYNTHETIC_TO_PRODUCTION_DENIED` decision to the canonical write and keep the
  adapter check.
- `aion-infra`: no new services; secrets for inbound provider webhooks only.

## Follow-up Decisions

- **Two-way sync and conflict resolution** (before any provider field becomes
  writable from both sides).
- **Customer merge and dedupe policy** (matching rules, confidence thresholds,
  human review).
- **Retention periods and erasure SLA** per data category.
- **Twenty version re-pin**: replace the v0.40.0 default and Gate C invariants
  with a pinned current release after a compatibility benchmark.
- **Licence review** of Twenty before any commercial distribution of a
  modified build.
- **AION OS views**: when to replace individual Twenty views with AION-native
  ones.
