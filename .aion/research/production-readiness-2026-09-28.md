# AION production readiness — 2026-09-28

**Status:** Assessment complete. Operating posture: tightly supervised internal
production only.  
**Owner:** AION Systems — P0/GROW (certify); Holding consumes outcomes  
**Supersedes checkpoint:** Track A go-live snapshot (2026-09-14) and RES-002
certify-first recommendation (2026-09-25) for *current position* — RES-002
remains the accepted strategy.  
**Canonical follow-through:** [GE-001](../missions/GE-001-governed-execution-certify.md),
[governed-execution-certify](../../roadmap/governed-execution-certify.md)  
**Notion:** [AION Production Readiness — September 28, 2026](https://www.notion.so/3e95b5fa4f8f81b2a9c0f88715e1b3de)

---

## Verdict

AION is ready for **tightly supervised internal production work**, but **not**
for repeatable multi-client or autonomous revenue operations.

The platform now does materially more than smoke-only hosting: it has executed
governed live CRM work, authenticated the Operator Console, enforced tenant
identity, delivered real alerts, and restored production backups in isolation.
The next maturity gate — **one clean, restart-safe, permission-scoped workflow
with economic evidence** — remains open.

| Evidence level | Current position |
|---|---|
| Documented architecture | Strong |
| Implemented platform code | Strong |
| Automated platform tests | Strong, except Products `main` is red |
| Repository-recorded live operations | Meaningful but release-fragmented |
| Repeatable revenue production | Not established |
| Broad client readiness | Not ready |

---

## Changes since the previous review

### Major gates cleared

- **Approval plane:** Runtime PRs [#46](https://github.com/Ceoloo/aion-runtime/pull/46)
  and [#48](https://github.com/Ceoloo/aion-runtime/pull/48) were deployed as git
  `10de0663`; operator evidence records authenticated tenant denials and
  server-derived approval identity.
- **Operator Console authentication:** production Console uses a Vercel Services
  BFF with an HTTP-only signed session and server-side Runtime bearer token.
  Login → real Runtime data → logout worked in production. Logout remains
  client-side invalidation only; captured tokens remain valid for ~12 hours.
  Products [#36](https://github.com/Ceoloo/aion-products/pull/36).
- **Detection and recovery:** controlled failure alerts, recovery alerts,
  restart classification, and isolated backup restores were evidenced and
  merged. Runtime/config/Supabase restore checks are scheduled.
  Infra [#14](https://github.com/Ceoloo/aion-infra/pull/14),
  [#16](https://github.com/Ceoloo/aion-infra/pull/16).
- **Tenant isolation:** Data supplies tenant-scoped database checkouts and
  real-Postgres RLS tests; Runtime pins aligned Core/Data contracts and
  propagates tenant context. Data [#24](https://github.com/Ceoloo/aion-data/pull/24),
  Runtime [#55](https://github.com/Ceoloo/aion-runtime/pull/55).
- **Identity and authorization gaps:** Runtime closed tenant gaps on
  commands/outcomes and prevented mission callers from overriding a registered
  agent’s durable authority. Runtime [#50](https://github.com/Ceoloo/aion-runtime/pull/50),
  [#51](https://github.com/Ceoloo/aion-runtime/pull/51).
- **Operator Loop implementation:** approval-linked continuation, persisted
  workflow context, outcome recording, and metrics landed.
  Runtime [#53](https://github.com/Ceoloo/aion-runtime/pull/53),
  Products [#39](https://github.com/Ceoloo/aion-products/pull/39).
- **Current Runtime CI:** proof-suite repair and synthetic-production boundary
  branches passed CI. Runtime [#56](https://github.com/Ceoloo/aion-runtime/pull/56),
  [#57](https://github.com/Ceoloo/aion-runtime/pull/57).

### Material finding: live capability demonstrated; clean certification failed

A production Operator Loop attempt wrote a synthetic qualification note onto a
real Annfiera CRM contact. That demonstrates genuine live business execution —
and invalidates that run as clean production evidence.

Full incident record:
[`aion-runtime` OL-001 synthetic CRM write](https://github.com/Ceoloo/aion-runtime/blob/main/docs/incidents/2026-09-27-ol001-synthetic-crm-write.md).

Runtime [#57](https://github.com/Ceoloo/aion-runtime/pull/57) adds a boundary
that rejects synthetic/test evidence before it reaches known live customer
records. **CI passed and the merge (`20fe3a4`) is on Runtime `main`.** Repo
validation on 2026-09-28 found **no GitHub deployment record proving that
commit is the currently serving VPS image**. The last Infra-pinned production
digest remains 2026-09-22 (`10de0663` /
`sha256:e3fef42f972b708990330bc6102b584e5575457314e2480af4c0cdd5241a60b0`).
The live CRM note has not been cleaned up.

| Claim | Position |
|---|---|
| Live execution capability | Demonstrated |
| Clean governed Operator Loop certification | Not passed |
| Production deployment of corrective boundary | Not evidenced |
| Client-data contamination risk | Contained in code, not yet proven contained live |

---

## Current production blockers and risks

1. **No single evidenced current release.** Core/Data/Runtime have moved
   substantially since the Sep 22 digest (RLS context, Operator Loop
   continuation, proof fixes, synthetic-write boundary). A merged change is
   not a deployed control. Before another live acceptance run, publish one
   manifest with Runtime image digest + SHA, Core/Data pins, migration set,
   Products SHA, Infra SHA, and verified production identity. Draft:
   [`releases/governed-execution-candidate.manifest.json`](../../releases/governed-execution-candidate.manifest.json).

2. **Products `main` is red.** Core pin alignment introduced browser
   `node:crypto` imports that break the isolated preview build. Fix is ready
   and CI-green on Products [#44](https://github.com/Ceoloo/aion-products/pull/44)
   (still open / MERGEABLE as of this assessment). Blocks calling the combined
   Console/Copilot source releasable.

3. **Revenue Copilot active sessions do not survive restart in production.**
   The serving HTTP process has historically retained live `Session` objects in
   an in-memory `Map`. Durable storage was best-effort at finalization; accepted
   turns and feedback were not checkpointed and reconstructed. Draft mitigation:
   Products [#45](https://github.com/Ceoloo/aion-products/pull/45) persists
   active turns, feedback, state, and execution lineage through Runtime; restart
   restores committed turns without replaying earlier AI work; interrupted
   operations return `409 reconciliation_required`. Local typecheck + 82 tests
   and PR CI are green; the PR remains **draft** for review of checkpointed
   customer data and interrupted finish behavior. It does **not** establish
   exactly-once provider execution, client-facing Copilot authentication,
   production deployment of this change, or GE-001 PASS. Operator Loop Core
   persistence still does not by itself make interrupted Copilot conversations
   durable on the serving image. Older active-checkpoint work remains open
   (Data [#1](https://github.com/Ceoloo/aion-data/issues/1)).

4. **Console authentication is operational; Copilot authentication is not.**
   Do not expose Revenue Copilot to clients until authenticated caller
   identity, tenant binding, authorization, throttling, and session ownership
   checks exist.

5. **Retry and crash ambiguity remain.** A crash after a provider succeeds but
   before the execution row is saved can repeat a write. Operator
   reconciliation is required before retry. Acceptance still needs a
   deliberate restart at a governed pause and a provider-side reconciliation
   scenario.

6. **Tenant/worker permissions improved but depend on complete classification.**
   Core skips the data-class check when a call path declares no classes.
   Every production workflow must prove adapters declare tenant, resource, and
   data-class scope. New migrations and NULL-tenant backfill are not evidenced
   on production.

7. **Database TLS identity verification remains open.** Runtime and migration
   clients still use `rejectUnauthorized: false`.

8. **Rollback is image-safe; schema rollback compatibility is not proven.**
   Migrations run before the container roll — previous image must be tested
   against the newly migrated schema.

9. **Recovery documentation conflicts.** Architecture brief marks RECOVERABLE
   — VERIFIED, but DR drill results log is empty and recovery kit still marks
   portions of clean-host restoration untested. Backup restore is well
   evidenced; blank-VPS reconstruction is not repository-verifiable.

---

## Mission-001 production evidence

| Gate | Verified real-production evidence |
|---|---|
| ≥25 real evaluable conversations | Not found |
| ≥85% explicit-fact accuracy | Not found |
| ≥60% useful/acted-on rated interventions | Not found |
| ≥10 positive conversion events | Not found |
| ≥3 meaningful downstream conversions | Not found |
| Complete production lineage | Not found |

These are **evidence gaps**, not measured zeros. The green evaluation suite is
synthetic plumbing validation — not production validation. The contaminated
OL-001 CRM run cannot count toward these thresholds.

---

## Top three next actions (cash-flow priority)

1. **Certify one clean AION Systems revenue workflow.** Order: merge green
   Products [#44](https://github.com/Ceoloo/aion-products/pull/44) → deploy a
   pinned Runtime release containing [#57](https://github.com/Ceoloo/aion-runtime/pull/57)
   and identify the serving digest → clean **only** the identified Annfiera
   note (preserve the incident ledger) → run GE-001 on a fresh low-risk real
   record — not synthetic evidence on a client contact. Prove named human
   approval, denial without side effect, restart/resume under the same root
   execution, provider reconciliation, and a terminal outcome with separate
   fields for provider expense, operator time, pipeline value, and collected
   cash. **Provider expense** must come from a priced source or be stamped
   explicitly unverified — Runtime cost units alone do not certify economic
   execution. Keep any existing Annfiera payment separate from GE-001’s
   attributed outcome; it predates that acceptance run.

2. **Lock the release and recovery boundary.** Publish the cross-repository
   release manifest; audit/backfill NULL tenant rows; verify RLS with the
   application role; enable CA-verified database TLS; test the previous image
   against the migrated schema; reconcile clean-VPS DR documentation with the
   claimed completed drill.

3. **Finish authenticated, durable Revenue Copilot and begin the 25-call
   dataset.** Put Copilot behind authenticated tenant sessions, checkpoint
   every accepted turn and feedback event, reconstruct a session after a
   forced restart, then collect real AION Systems conversations with manual
   ground truth and complete outcome lineage.

---

## Operating implications

| Surface | Posture |
|---|---|
| VPS | Replaceable, digest-pinned execution host. Durable conversations, approvals, executions, costs, and outcomes belong in PostgreSQL; secrets and workers remain tenant- and task-scoped. |
| AION Holding | Aggregated outcomes and economics through reporting views. Holding visibility ≠ cross-tenant worker authority. |
| AION Systems — P0/GROW | Owns the first certified workflow and Mission-001 dataset. |
| Digital Empire — VALIDATE | May supply leads/distribution; no autonomous production workflows yet. |
| G-Star — INCUBATE | No custom Runtime investment. |
| Synapse | Remain paused. |
| Personal Ops | Protected and isolated from client/customer data. |
| Client operations | Supervised, named-human execution only until clean Operator Loop and Mission-001 evidence pass. |

### Explicitly deferred

Shared Rooms/Work Desks integration, OTEL/vendor observability, Okta
federation, Skills registry, autonomous messaging or appointment creation,
broad client onboarding, and further Desks expansion. Desks still lacks
evidence of a real purchase, signed webhook, entitlement delivery, refund,
and revenue record.

---

## Repo tip snapshot (validated 2026-09-28)

| Repo | `origin/main` tip | Notes |
|---|---|---|
| `aion-runtime` | `20fe3a4` (#57 merged) | Synthetic boundary on main; **not** evidenced as serving image |
| `aion-core` | `26f43e5` | Ahead of Runtime’s pinned Core (`52ecf40`) |
| `aion-data` | `cb29e7c` (#24 RLS) | Ahead of Runtime’s pinned Data (`5da145a`); migrations through `0013` |
| `aion-products` | `06eedbf9` (#44 merged) | `main` CI green (run 36488161210) |
| `aion-infra` | `3382b61` | Last digest evidence still Sep 22 `10de0663` |
| `aion-docs` | tip at assessment commit | This document |

---

## Bottom line

AION has crossed into real controlled operations, but this week showed exactly
why “governed” must mean more than an authorization decision. The next
milestone is a **clean, release-pinned, restart-tested execution that creates
real value without contaminating client state** — and records both its cost
and business outcome.
