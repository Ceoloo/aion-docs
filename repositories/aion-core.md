# aion-core

**Role:** The Company OS and control plane. `aion-core` **coordinates systems**.

## Owns

- Company OS primitives
- orchestration
- execution coordination
- agent interfaces
- workflow engine
- tool interfaces
- permissions and policy enforcement
- event contracts used during execution
- observability hooks
- shared platform packages
- runtime abstractions

## Does NOT own

`aion-core` must not become the **database**, **product UI**, the
**infrastructure repository**, a **CRM**, or **every department-specific
workflow**. Those belong to `aion-data`, `aion-products`, `aion-infra`, and the
products themselves respectively.

## Boundary intuition

If the answer to "what is this?" is:

- *"deciding what happens, who does it, and whether it's allowed"* → **aion-core**.
- *"the canonical shape and storage of a business entity"* → aion-data.
- *"a screen or product-specific flow"* → aion-products.
- *"where and how it runs"* → aion-infra.

## Key architectural references

- [../architecture/control-plane.md](../architecture/control-plane.md) — the
  orchestration model core implements.
- [../architecture/execution-layer.md](../architecture/execution-layer.md) — the
  common execution contract core owns.
- [../governance/agent-governance.md](../governance/agent-governance.md) — agents
  as governed workers.
- [../governance/permissions.md](../governance/permissions.md) — permission and
  policy enforcement.
- [../engineering/event-standards.md](../engineering/event-standards.md) — the
  execution event contracts core emits.

## Scope discipline

Core is the coordinator, not the doer and not the store. When a capability in
core starts holding canonical data or rendering product UI, the boundary has
drifted — resolve it, don't accept it. See
[dependency-rules.md](dependency-rules.md).

## Depend on ports, not storage

`aion-core` depends on **data contracts / ports**, never on a concrete database
or persistence implementation. `aion-data` provides the persistence adapters
behind those ports. The clean `core → data` dependency is a dependency on
*contracts*, not on a storage vendor — which keeps the control plane decoupled
from whatever engine is later chosen (a deferred, ADR-gated decision). See
[dependency-rules.md](dependency-rules.md#core-depends-on-data-contracts-not-storage).

> **Status:** **Phase 1 — AUTHORIZED.** Implementation may begin, bounded by the
> mission-scoped [critical caveat](../roadmap/build-order.md#the-critical-caveat)
> and closed by the
> [Phase 1 exit gate](../roadmap/build-order.md#phase-1-exit-gate--aion-core):
> stable, versioned contracts for Mission, Actor/Agent, Capability, Tool,
> Workflow/Run, Command, Event, Policy/Permission, Approval Gate, Result/Outcome
> reference, and execution telemetry — proven by tests to support one real
> mission end-to-end with no product-specific logic in core.
