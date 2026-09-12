# ADR-006: PostgreSQL as the Durable Store

- **Status:** Accepted
- **Date:** 2026-09-12
- **Decision Owners:** Architect
- **Related:** [ADR-001](ADR-001-greenfield-reset.md),
  [ADR-002](ADR-002-runtime-host-ownership.md),
  [ADR-005](ADR-005-package-dependency-direction.md)

## Context

ADR-001 deferred storage-engine selection. The platform nevertheless standardized
on PostgreSQL in `aion-data` (migrations `0001`–`0009`, `pg` adapters) and
`aion-runtime` (migrate job, readiness against Postgres). Leaving the choice
“deferred” while every durable path assumes Postgres is architecture fiction.

## Decision

**PostgreSQL is the durable system of record for AION platform state**
(missions, runs, executions, events, approvals, catalog, autonomy grants,
side-effect ledger, and product façades persisted via Data such as
`revenue_sessions`).

1. Canonical migrations live only in `aion-data`.
2. Runtime runs those migrations; it does not own schema content.
3. Core remains DB-agnostic (ports + in-memory adapters).
4. Alternate stores (analytics warehouses, object stores, brokers) remain
   out of scope until a separate ADR selects them for a named capability.

## Alternatives Considered

- **Alternative A — Continue deferring**  
  **Rejected:** code already locked the choice; docs must not pretend otherwise.

- **Alternative B — SQLite / embedded for all environments**  
  **Rejected:** insufficient for multi-tenant Runtime + concurrent workers.

- **Alternative C — Document store as primary**  
  **Rejected:** relational constraints and migrations already encode invariants
  the control plane relies on.

## Consequences

### Positive

- Honest constitution; infra/runtime can assume Postgres capability.
- Clear home for migrations and backup/restore runbooks.

### Negative

- Vendor/engine coupling at the data plane (mitigated by Core ports).
- Future multi-region or serverless-Postgres choices still need ops ADRs.

## Implementation Notes

- Infra deployment profiles must provision Postgres (or a compatible managed
  offering) before Runtime ready checks can pass.
- Do not add a second primary OLTP store beside Postgres without a new ADR.

## Follow-up Decisions

- Managed Postgres vendor / topology (still capability-first in infra docs).
- Analytics/warehouse selection when learning-loop volume requires it.
