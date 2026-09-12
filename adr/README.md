# Architecture Decision Records (ADRs)

An ADR captures a significant architectural decision: the context, the decision,
the alternatives considered, and the consequences. ADRs are how AION makes its
reasoning **durable and reviewable** — so future engineers (and agents)
understand not just *what* was decided but *why*.

## When to write an ADR

Write an ADR when a decision:

- alters a **repository boundary** or **ownership**;
- changes a **cross-repo contract** (event, data, or API);
- **selects or replaces a technology/vendor** previously left open;
- changes a **governance rule** (permission model, gate policy, risk mapping);
- sets a **precedent** future work will follow.

**Do not** write an ADR for routine, reversible, in-boundary changes. ADR noise
is as harmful as no ADRs. See
[../governance/change-management.md](../governance/change-management.md).

> Guidance: **Do not create unnecessary ADRs yet.** Prefer extending existing
> decisions; only add an ADR when a boundary or precedent actually changes.

## The standard

Every ADR follows the structure in
[ADR-000-template.md](ADR-000-template.md):

```
# ADR-XXX: Title
Status:
Date:
Decision Owners:
## Context
## Decision
## Alternatives Considered
## Consequences
### Positive
### Negative
## Implementation Notes
## Follow-up Decisions
```

## Status values

- **Proposed** — under discussion.
- **Accepted** — decided and in force.
- **Superseded by ADR-YYY** — replaced by a later decision (never deleted).
- **Deprecated** — no longer applicable.

ADRs are **append-only history**: a wrong decision is superseded by a new ADR,
not edited away.

## The capability-over-vendor rule

When a technology is not yet chosen, **do not lock a vendor in an ADR
prematurely.** Record the required capability and defer:

> Bad: *"AION must use Kafka."*
> Better: *"AION requires durable asynchronous event delivery once
> throughput/reliability requirements justify a broker. The implementation will
> be selected through an ADR."*

## Index

| ADR | Title | Status |
|---|---|---|
| [ADR-000](ADR-000-template.md) | Template | Template |
| [ADR-001](ADR-001-greenfield-reset.md) | Greenfield Reset | Accepted |
| [ADR-002](ADR-002-runtime-host-ownership.md) | Runtime Host Ownership | Accepted |
| [ADR-003](ADR-003-execution-gateway-into-runtime.md) | Reconcile Execution Gateway into Runtime | Accepted |
| [ADR-004](ADR-004-aion-desks-repo-ownership.md) | `aion-desks` Repository Ownership | Accepted |
| [ADR-005](ADR-005-package-dependency-direction.md) | Package Dependency Direction (Ports) | Accepted |
| [ADR-006](ADR-006-postgres-durable-store.md) | PostgreSQL as Durable Store | Accepted |
| [ADR-007](ADR-007-products-runtime-http-client.md) | Products → Runtime HTTP Client | Accepted |
| [ADR-008](ADR-008-runtime-composition-scope.md) | Runtime Composition-Root Scope | Accepted |
