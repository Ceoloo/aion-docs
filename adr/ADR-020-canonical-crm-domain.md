# ADR-020: AION Canonical CRM Domain (customer and revenue system of record)

- **Status:** Proposed
- **Date:** 2026-10-26
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

Facts as of 2026-10-26:

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
7. **Cash flow comes first.** GE-001 is the first certified revenue workflow.
   A proposal to re-scope it to Twenty after CRM-001 Gate D is under separate
   review (aion-docs #107, AIO-37, AIO-109). Nothing in this ADR may add to
   the GE-001 critical path, whichever CRM it runs on.

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
   | `crm_events` | Append-only journal of every canonical change and every inbound provider change; holds no personal values (see §9) |

   Delivery is staged (see Implementation Notes). Stage 1 is the identity spine
   only: customers, contacts, companies, relationships, consents, the identity
   map and the event journal.

3. **AION owns ids.** Ids are opaque, prefixed and AION-minted (for example
   `cust_…`, `cont_…`, `comp_…`, `opp_…`). The canonical provider-to-AION
   mapping lives in `crm_external_identity_map`. The side-effect ledger also
   keeps the provider ids of governed writes as evidence, but it is not a
   lookup source. The map has two uniqueness rules among **active** rows:
   - one AION id per `(tenant_id, provider, provider_workspace,
     provider_object, provider_id)`, so two AION records cannot claim the same
     provider object;
   - one provider id per `(tenant_id, provider, provider_workspace,
     provider_object, aion_id)`, so one AION record has at most one live
     counterpart per provider object type.

   A mapping that is replaced (for example after a provider-side merge or
   re-import) is closed with `valid_to` and a reason, not deleted, and stays
   as history. Only rows with no `valid_to` are active. Agents and products
   refer to customers by AION id. Payment processors stay authoritative for
   settlement; AION stores references and verified outcomes (existing
   `outcomes`), not card or bank data.

4. **Field authority is a static matrix, and sync starts one-way.** A
   field-authority matrix ships with the schema and assigns every canonical
   field exactly one authority, per provider:
   - **AION-authoritative:** identity, tenant, lifecycle stage, consent,
     dedupe/merge state, qualification label, and every field written by a
     governed Runtime capability.
   - **Provider-authoritative:** named fields a human maintains in the
     provider UI that AION does not govern.

   Authority is fixed by the matrix. It never changes because an agent or a
   human edited a field last. The matrix changes only through a reviewed
   change to the schema contract (migration plus matrix update in the same
   PR).

   Stage 1 to Stage 3 sync is **outbound projection**: canonical changes are
   pushed to providers through the existing `CrmBackend` adapters. Inbound
   provider changes are recorded in `crm_events` with provenance:
   - to a provider-authoritative field: applied to the canonical record;
   - to an AION-authoritative field: **not applied**. It is recorded as a
     conflict and surfaced for a governed reconcile decision. A Twenty edit
     never silently overrides an AION-authoritative value, and projection does
     not silently overwrite the human's edit either until reconcile decides.

   There is no last-write-wins. Full two-way conflict resolution needs its own
   ADR.

5. **Synchronization is durable.** Both directions are built on these
   mechanisms; none is optional:
   - **Transactional outbox.** A canonical write and its outbox entry commit
     in the same database transaction. Nothing is projected from an
     uncommitted change, and no committed change is lost.
   - **Idempotent projection.** Each projection is keyed by (outbox entry,
     provider, record version) and executed through `external_side_effects`
     with that key, so retries cannot write twice.
   - **Inbound deduplication.** Inbound events are unique per (provider,
     provider workspace, provider event id); replays are no-ops.
   - **Version checks.** Every canonical record carries a monotonically
     increasing version. Writes use optimistic concurrency, projections carry
     the version they reflect, and stale inbound changes are rejected and
     journaled.
   - **Retry queue.** Failed projections retry with backoff to a bounded
     limit, then move to a dead-letter state visible to operators.
   - **Ambiguous writes.** A projection whose provider outcome is unknown is
     reconciled by reading the provider before any retry (the GE-001 row 9
     rule). It is never blindly repeated.
   - **Reconciliation.** A scheduled job compares canonical records with their
     mapped provider records and journals every drift it finds; it repairs
     only through the rules above.

6. **All access goes through Runtime.** Canonical entities are read and
   written only through governed Runtime capabilities (ADR-003). Agents and
   products never receive SQL access, connection strings or provider API keys.
   Canonical operations extend the existing `crm.*` catalog rather than
   creating a parallel tool set. Proposed additions, with tiers to be fixed in
   the catalog PR:

   | Capability | Purpose | Proposed tier |
   |---|---|---|
   | `crm.customer.resolve` | Find a customer by identifiers; never creates | R1 |
   | `crm.customer.create` | Create after resolve finds no match | R2 |
   | `crm.customer.merge` | Merge duplicates, keeping both histories | R3, **disabled** until the merge/dedupe policy ADR is accepted |
   | `crm.customer.context` | Authorised customer, relationship and consent view | R1 with field-level permissions, masking and read audit |
   | `crm.customer.timeline` | Ordered events, activities and outcomes | R1 with field-level permissions, masking and read audit |
   | `crm.lead.qualify` | Record label plus evidence reference | R2 |
   | `crm.opportunity.advance` | Stage transition | R2 |
   | `crm.activity.record` | Note, call or task on a customer | **R2 in production** initially, with mandatory data-class check; lowering it needs evidence and a catalog review |
   | `crm.outreach.prepare` | Reviewable draft; never sends | R2 |

   Sending stays on `crm.message.send` / `crm.conversation.send` (R3).

   R1 reads are not unrestricted. A grant names the fields it may return;
   fields outside the grant are masked, not omitted silently; and every read
   is audited with actor, customer id, fields returned and execution id.

7. **The evidence-class guard moves to the canonical write.** Every canonical
   row and event carries `data_class` (`production`, `synthetic`, `test`,
   `fixture`). The canonical write path refuses to mix classes on one customer
   and refuses to project non-`production` data to a live provider, returning
   `SYNTHETIC_TO_PRODUCTION_DENIED`. The per-provider guard in the CRM adapter
   stays as defence in depth. A new provider therefore inherits the boundary
   without code of its own.

8. **Tenant isolation is on from the first table.** Required, not optional:
   - every canonical table has `tenant_id NOT NULL`, `ENABLE` and
     `FORCE ROW LEVEL SECURITY`, with policies keyed on `aion.tenant_id`
     that fail closed when it is unset;
   - tenant context is **transaction-scoped** (`SET LOCAL` /
     `set_config('aion.tenant_id', …, true)`) inside the same transaction as
     the query. Session-level tenant settings are not allowed, because pooled
     connections are reused across tenants;
   - the application role does not own the tables and has `NOBYPASSRLS`; it
     is never a superuser. Migrations run under a separate role;
   - tests run on a pooled connection that serves tenant A then tenant B, and
     prove that no row crosses, and that an unset context returns no rows.

   The existing `0010_tenant_rls` scaffold does not meet this bar on its own;
   canonical tables must.

9. **Consent and erasure are enforced, not documented.**
   - **Consent is per channel** (for example email, SMS, phone, messaging
     apps). Runtime checks the channel's consent before any outreach or send
     capability runs and before projecting a contact point to a provider that
     can use that channel. Withdrawal is journaled and projected like any
     other change.
   - **Retention has owners.** A retention register lists each data category,
     its retention period and the accountable owner role. Expiry runs as a
     governed action.
   - **The journal holds no personal values.** A `crm_events` row records
     the AION id, event type, field names, record version, actor, provenance
     and time, plus a digest of changed values. It never stores personal
     values. Raw inbound provider payloads, needed briefly for
     reconciliation, go to a separate payload store keyed by event id, with a
     short retention period from the register. They are deleted on erasure
     and on expiry. The journal stays append-only and survives erasure
     without containing personal data.
   - **Erasure is verified.** Erasure is a governed action. It removes
     personal fields from the canonical record and deletes the subject's raw
     payloads, records a non-personal erasure event in `crm_events`, and issues
     a deletion to every mapped provider through the projection path. Each
     provider deletion is confirmed by read-back.
   - **Failed or offline deletions are tracked.** A provider that is offline
     or refuses the deletion leaves the request in an `erasure_pending` state
     with a deadline. It retries through the retry queue and escalates to a
     named human owner when the deadline passes. The request closes only when
     every mapped provider is confirmed.

10. **No new service.** Schema and repositories live in `aion-data`, ports and
   entity types in `aion-core`, governed capabilities and projection in
   `aion-runtime`. No `aion-crm` microservice or repository (consistent with
   ADR-019 and the reuse audit).

11. **This does not block CRM-001 or GE-001.** CRM-001 Gates A–D and GE-001
   proceed on their own tracks. GE-001 evidence keeps provider ids in the
   side-effect ledger. When Stage 1 lands, a governed backfill maps those ids
   into `crm_external_identity_map` without rewriting the ledger. Records
   touched by the OL-001 incident are excluded from any backfill or migration
   until verified clean.

12. **Production is a separately certified release.** Canonical personal
    data is not written in production until Stage 4 passes as its own
    release, with its own manifest and an evidence pack reviewed by someone
    other than its implementer. A GE-001 PASS is necessary but not sufficient.
    The pack must show:
    - production RLS verified with the application role, and the AIO-35
      previous-image finding closed;
    - migration forward, plus the previous Runtime image operating against the
      migrated schema (or a verified rollback mitigation);
    - cross-tenant isolation per §8, including the pooled-connection test;
    - sync per §5: outbox, idempotent projection, duplicate inbound events,
      provider outage, ambiguous write and reconciliation;
    - consent enforcement and a verified erasure across providers per §9;
    - a complete retention register: a period and owner role for every
      personal data category, including the raw payload store;
    - a rollback plan to provider-only operation without loss of governance
      records;
    - the reviewed field-authority matrix.

    Non-production work can start once this ADR is accepted. Production
    migrations and cutover are not authorized by this ADR.

13. **Providers are pinned and benchmarked, and kept at arm's length.** Any
    provider projection targets a pinned provider version that has passed a
    compatibility benchmark. For a self-hosted provider such as Twenty, the
    pin is the deployed image digest. For a SaaS provider such as GHL, the pin
    is the API version the adapter sends (for example GHL's `Version` request
    header) plus the object fields the adapter reads and writes. The
    benchmark is a contract test of those fields against the pinned version,
    re-run before any version change. Twenty runs as a separate process reached only
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
- Durable sync (outbox, versioning, retries, reconciliation) is real
  engineering work before any provider sees canonical data; provider outages,
  duplicates and ambiguous writes must be handled and tested.
- Field-level permissions, masking and read audit add work to every read
  capability.
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
| 1 | Identity spine only (non-prod): customers, contacts, companies, relationships, consents, identity map, events, `data_class`, tenant security per §8 | See Stage 1 acceptance below |
| 2 | Durable sync per §5: outbox, projection to Twenty and GHL, inbound journal | Outbox, idempotency, inbound dedupe, version, retry, ambiguous-write and reconciliation tests; provider outage tests |
| 3 | Leads, opportunities, activities; remaining capabilities; consent and erasure per §9 | Catalog entries with tiers; RCAOS AT-1…AT-7 re-run against canonical records; erasure verification across providers |
| 4 | Production release | Independently certified per §12; governed backfill of GE-001 ids (OL-001 records excluded) |

**Stage 1 acceptance.** Stage 1 stays on the identity spine. It does not
include opportunity pipelines, messaging or full CRM synchronization. It passes
when, in a non-production environment:

1. a customer is assigned an AION-owned id through `crm.customer.create`;
2. that id is linked to at least two provider ids (fake GHL and fake Twenty)
   in `crm_external_identity_map`;
3. after a Runtime restart, `crm.customer.resolve` by either provider id and
   `crm.customer.context` return the same customer within the owning tenant;
4. the same calls from another tenant return nothing, including on a pooled
   connection that served the owning tenant first, and with tenant context
   unset;
5. synthetic and production `data_class` rows cannot be mixed on one customer;
6. Stage 1 migrations, Core ports and Runtime capabilities pass the repo CI.

**Per repository**

- `aion-data`: next free migration numbers on `main` (0014 is the latest);
  Postgres repositories; tenant security per §8 from creation; outbox, version
  columns and inbound-event uniqueness per §5; the field-authority matrix as a
  versioned artifact next to the schema.
- `aion-core`: entity types, repository ports, capability catalog entries and
  risk tiers; the `data_class` rule as a pure function.
- `aion-runtime`: governed handlers for the new capabilities; transaction-
  scoped tenant context on every canonical query; outbox-driven projection that
  reuses `CrmBackend` and `external_side_effects`; inbound provider endpoint
  (authenticated, deduplicated) writing to `crm_events`; consent checks before
  outreach and channel projection; read audit and field masking for R1 reads;
  move the `SYNTHETIC_TO_PRODUCTION_DENIED` decision to the canonical write and
  keep the adapter check.
- `aion-infra`: no new services; secrets for inbound provider webhooks only.

## Follow-up Decisions

- **Two-way sync and conflict resolution** (before any provider field becomes
  writable from both sides).
- **Customer merge and dedupe policy** (matching rules, confidence thresholds,
  human review). `crm.customer.merge` stays disabled until it is accepted.
- **Retention register**: periods, owner roles and erasure deadlines per data
  category.
- **Twenty version re-pin**: replace the v0.40.0 default and Gate C invariants
  with a pinned current release after a compatibility benchmark.
- **Licence review** of Twenty before any commercial distribution of a
  modified build.
- **AION OS views**: when to replace individual Twenty views with AION-native
  ones.
