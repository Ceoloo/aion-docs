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

> **Status:** Not yet built. `aion-core` is **Phase 1** of the
> [build order](../roadmap/build-order.md). Do not begin implementation as part
> of establishing `aion-docs`.
