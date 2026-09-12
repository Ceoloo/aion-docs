# ADR-008: Runtime Composition-Root Scope

- **Status:** Accepted
- **Date:** 2026-09-12
- **Decision Owners:** Architect
- **Related:** [ADR-002](ADR-002-runtime-host-ownership.md),
  [ADR-003](ADR-003-execution-gateway-into-runtime.md),
  [`../architecture/runtime-slim-down.md`](../architecture/runtime-slim-down.md)

## Context

ADR-002 defines `aion-runtime` as a **thin composition root**. ADR-003 places
the Execution Gateway HTTP surface on that same process. Those decisions are
compatible only if the gateway stays **ingress + wiring**.

In the current codebase the gateway module has grown into a large application
layer (commands, missions, economics, evaluations, autonomy, CRM adapter
orchestration, Implementation Engine surfaces, revenue sessions). Mission
capability/risk tables and GHL adapters also live in Runtime. That weight is the
primary long-term threat to composability: Runtime becomes the de facto
monolith while still being labeled “composition root.”

## Decision

**Runtime may host HTTP ingress and composition. It must not become the home of
expanding domain/product logic.**

Allowed in `aion-runtime`:

- process boot, config validation, health/readiness, graceful shutdown
- wiring Core orchestrators to Data adapters (composition root)
- migration entrypoint invoking `aion-data` migrations
- Execution Gateway as **thin HTTP adapter** translating requests into Core
  commands / Data port calls
- small, explicitly owned integration adapters required for platform-level
  external side effects (until extracted), behind Core policy

Not allowed to accumulate in Runtime:

- new product engines, UI backends, or SKU logic (belong in `aion-products`)
- canonical schema / migration content (belong in `aion-data`)
- orchestration/policy algorithms (belong in `aion-core`)
- cloud provider SDKs (belong in `aion-infra` profiles)
- unbounded “god file” domain handlers — new verticals must not grow
  `gateway.ts` without extraction

**Slim-down plan** (binding intent, incremental execution): keep `/v1/*` routes
as ingress; peel domain handlers, IE, and CRM adapter bulk into clearly named
modules with ownership targets documented in
[`architecture/runtime-slim-down.md`](../architecture/runtime-slim-down.md).
Extractions packages remain Runtime-deployed until a later ADR moves them.

## Alternatives Considered

- **Alternative A — Split Gateway into a seventh service**  
  **Rejected by ADR-003;** reaffirmed here.

- **Alternative B — Accept Runtime as the application monolith**  
  **Rejected:** recreates pre-greenfield coupling; blocks multi-product reuse.

- **Alternative C — Move Gateway into Core**  
  **Rejected:** Core must stay embeddable/DB-agnostic; HTTP host concerns belong
  at the composition root.

## Consequences

### Positive

- Restores ADR-002 meaning without repealing ADR-003.
- Gives reviewers a reject criterion for Runtime PRs that add product bulk.

### Negative

- Refactor cost; transitional modules will look “halfway extracted.”
- Some CRM adapter code remains in Runtime until an extraction ADR.

## Implementation Notes

- Follow [`runtime-slim-down.md`](../architecture/runtime-slim-down.md).
- Prefer extending Core ports over Runtime→DataLayer field poking
  ([contract-hygiene](../engineering/contract-hygiene.md)).

## Follow-up Decisions

- Whether GHL adapter becomes `aion-runtime` submodule forever or a separate
  adapter package published for the Runtime image only.
- Product composition roots vs platform Runtime for workforce consoles
  (deferred from ADR-002).
