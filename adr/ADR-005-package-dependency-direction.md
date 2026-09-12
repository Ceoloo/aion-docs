# ADR-005: Package Dependency Direction (Ports Pattern)

- **Status:** Accepted
- **Date:** 2026-09-12
- **Decision Owners:** Architect
- **Related:** [ADR-001](ADR-001-greenfield-reset.md),
  [ADR-002](ADR-002-runtime-host-ownership.md),
  [`../repositories/dependency-rules.md`](../repositories/dependency-rules.md)

## Context

Constitution diagrams and `dependency-rules.md` historically drew
`aion-core --> aion-data` and stated that **data must never depend on core**.

The implemented ports pattern is the opposite at the *package* layer:

- `@aion/core` owns Zod contracts, orchestration, policy, and **persistence ports**.
- `@aion/data` implements those ports against Postgres and therefore **depends on
  `@aion/core`**.
- `@aion/core` has **zero** dependency on `@aion/data` (database-agnostic kernel).

Leaving the docs inverted caused agents to “fix” the graph toward a core→data
import (or to treat the working ports pattern as illegal drift).

## Decision

**We ratify the ports package direction:**

1. **`aion-data` may depend on `aion-core`** (contracts + ports only).
2. **`aion-core` must not depend on `aion-data`** (or Postgres / `pg`).
3. Logical ownership remains: Core owns *decision/contract* semantics; Data owns
   *durability and migrations*.
4. `repositories/dependency-rules.md` and repository diagrams must show
   `DATA --> CORE`, not `CORE --> DATA`, for package edges.

## Alternatives Considered

- **Alternative A — Force core→data package dependency**  
  Core imports Data repositories.  
  **Rejected:** destroys DB-agnostic kernel; couples policy to Postgres; blocks
  in-memory adapters used for tests.

- **Alternative B — Share no package edge; duplicate types**  
  Data re-defines all shapes.  
  **Rejected:** guarantees schema fork and dual validation.

- **Alternative C — Keep docs saying core→data “conceptually”**  
  **Rejected:** agents treat the table as package law; ambiguity caused the
  greenfield-class drift we are preventing.

## Consequences

### Positive

- Docs match the running system and ADR-002’s DB-agnostic Core invariant.
- Clear rule for new ports: define in Core, implement in Data.

### Negative

- Older prose/diagrams that said “core depends on data” must be edited (this
  change set).
- Readers must distinguish *ownership downward* (products consume platform)
  from *package arrow* (adapters depend on contracts).

## Implementation Notes

- Update [`dependency-rules.md`](../repositories/dependency-rules.md) and
  [`repositories/README.md`](../repositories/README.md).
- Extend Core ports where Runtime currently bypasses them via raw `DataLayer`
  (tracked in [contract-hygiene](../engineering/contract-hygiene.md)).

## Follow-up Decisions

- Whether products keep an optional `@aion/data` package dependency or Runtime
  remains the sole durable façade ([ADR-007](ADR-007-products-runtime-http-client.md)).
