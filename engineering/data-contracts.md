# Data Contracts

A data contract is the explicit, owned definition of a piece of data — its
schema, its meaning, its owner, and its evolution rules. Data contracts are how
[explicit ownership](principles.md) becomes enforceable across repositories.

## Ownership

- **Canonical data contracts are owned by `aion-data`.** A business entity —
  `lead`, `payment`, `deployment` — has exactly one canonical schema, defined
  once. See [../repositories/aion-data.md](../repositories/aion-data.md).
- **Consumers read the contract; they do not fork it.** Products and services
  may cache or project canonical data, but the definition lives in one place.
- **No repository invents a canonical entity owned by another.** This is a
  [dependency rule](../repositories/dependency-rules.md).

## The six data kinds have contracts too

Beyond canonical entities, each of the six data kinds
([data-layer](../architecture/data-layer.md)) has contract expectations:

| Kind | Contract expectation |
|---|---|
| Operational state | Schema + which events drive its changes. |
| Events | Name + payload shape ([event-standards](event-standards.md)). |
| Memory | Shape + retention/scoping rules. |
| Lessons | Shape + link to originating outcomes. |
| Recommendations | Shape + link to originating lessons. |
| Analytics | Definition + the events/state it derives from. |

## Evolution rules

1. **Additive by default.** New optional fields are safe. Removing or repurposing
   a field is breaking.
2. **Breaking changes are versioned and migrated.** A migration path is provided;
   the change is [ADR](../adr/README.md)-backed if architecturally significant.
3. **Lineage is preserved.** Derived data (outcomes, lessons, analytics) must
   remain traceable to its source through a schema change. See
   [../architecture/data-layer.md](../architecture/data-layer.md).
4. **Migrations are owned by `aion-data`** and run through
   [change management](../governance/change-management.md).

## Classification

Every data contract carries a **classification** that drives access under the
[security model](../architecture/security-model.md) and
[permissions](../governance/permissions.md). An identity may read a class only
if its scope allows it.

## Deferred tooling

Schema format, migration tooling, and storage engine are chosen by `aion-data`
via ADR when a mission requires them. This standard governs *that* data is
contracted, owned, classified, and evolved safely — not the tool.

## Invariants

- **One canonical contract per entity, owned by `aion-data`.**
- **Consumers read; they never fork canonical definitions.**
- **Additive-safe; breaking changes are versioned and migrated.**
- **Every contract is classified and its lineage preserved.**
