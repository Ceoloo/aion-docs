# aion-data

**Role:** The canonical data foundation and system of record. Data ownership
must be **explicit**.

## Owns

- canonical schemas
- migrations
- event persistence
- memory models
- outcome records
- learning records
- analytics contracts
- data governance
- database access patterns
- durable business context
- data lineage

## Does NOT own

Orchestration (aion-core), product UI (aion-products), or infrastructure
provisioning (aion-infra). `aion-data` defines *what the data is and how it is
governed*, not *what decides to write it* or *where it runs*.

## The rule that matters most

**Do not allow every repository to independently invent canonical business
entities.** A `lead`, a `payment`, a `deployment` — each has exactly one
canonical definition, here. Other repositories read it, project it, or cache it;
they never fork it.

## The six data kinds

`aion-data` keeps these distinct — never collapsed into one generic "memory"
store (full definitions in
[../architecture/data-layer.md](../architecture/data-layer.md)):

operational state · events · memory · lessons · recommendations · analytics.

## Key architectural references

- [../architecture/data-layer.md](../architecture/data-layer.md) — the data
  model and the six kinds.
- [../architecture/learning-loop.md](../architecture/learning-loop.md) — how
  outcomes become lessons and memory.
- [../engineering/event-standards.md](../engineering/event-standards.md) — events
  as past-tense facts.
- [../engineering/data-contracts.md](../engineering/data-contracts.md) — how data
  contracts are defined and evolved.

## Deferred technology choices

Storage engines, event brokers, and analytics stores are chosen via ADR when a
mission requires them — not pre-selected. The required *capability* is
documented; the vendor is not locked prematurely.

> **Status:** Not yet built. `aion-data` is **Phase 2** of the
> [build order](../roadmap/build-order.md).
