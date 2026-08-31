# aion-products

**Role:** The products AION delivers — built **on** the platform, not rebuilding
it.

## Owns

- customer-facing products
- internal operational products
- product seeds
- experiments that have passed the appropriate [mission gate](../missions/lifecycle.md)
- interfaces built using the core platform and canonical data contracts

## Does NOT own

Platform primitives (aion-core), canonical data definitions (aion-data), or
infrastructure (aion-infra). A product may hold logic specific to itself; it may
not hold logic that belongs to the platform.

## The rule that matters most

**Products consume platform primitives instead of rebuilding infrastructure
locally.** A product calls the control plane for governed actions, reads
canonical data, and emits events through owned paths — it does not embed its own
orchestrator, permission system, or canonical entity. See
[../architecture/product-layer.md](../architecture/product-layer.md).

## Missions gate products

Every product traces to a mission. Product seeds and experiments enter this
repository only after passing the appropriate gate, and they advance, iterate,
or are killed based on validated outcomes. See
[../missions/lifecycle.md](../missions/lifecycle.md).

## Key architectural references

- [../architecture/product-layer.md](../architecture/product-layer.md) — how
  products relate to the platform.
- [../missions/lifecycle.md](../missions/lifecycle.md) — the mission lifecycle
  products move through.
- [../roadmap/platform-maturity.md](../roadmap/platform-maturity.md#platformization-rule)
  — when a product capability may be promoted to a platform primitive.

## Platformization is deliberate

A successful product component does **not** automatically become part of
`aion-core`. Promotion happens only under the
[platformization rule](../roadmap/platform-maturity.md#platformization-rule).

> **Status:** Not yet built. The first production mission/product is **Phase 4**
> of the [build order](../roadmap/build-order.md).
