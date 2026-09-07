# ADR-003: Reconcile Execution Gateway into Runtime

- **Status:** Accepted
- **Date:** 2026-09-06
- **Decision Owners:** Ceoloo (Founder / CEO)

## Context

Earlier AION work (company-os / Execution Gateway concepts) already designed
provider routing, approval, cost/latency/outcome logging, and replay. The Sep
2026 Execution Platform Progress Assessment states those capabilities are ~75%
designed and must be **reconciled into Runtime — not rebuilt as a second
gateway**.

Meanwhile:

- `aion-runtime` is the composition root (ADR-002) but historically exposed only
  health/release HTTP.
- Products (e.g. Revenue Copilot) still embed an in-memory control plane instead
  of submitting to durable Runtime.
- There was no single canonical **Execution Object** (`aion_execution`) or
  `agent://aion/{domain}/{role}/{id}` identity registry across planes.

## Decision

1. **The Execution Gateway is an HTTP surface of `aion-runtime`**, not a
   separate service or repository. Routes live beside health checks in the same
   process and write through Core → Data.
2. **Canonical contracts live in `aion-core`:** Execution Object + agent identity
   / autonomy fields. **Durability lives in `aion-data`** (`executions` table +
   actor identity columns). **Ingress lives in `aion-runtime`**.
3. Do **not** port legacy company-os gateway code by default (ADR-001 greenfield
   reset). Carry forward concepts via redesign against Core contracts.
4. Products and external workers (Grok, Kimi/Atlas, Codex, …) must become
   **clients of Runtime**, not owners of parallel orchestration state.

## Consequences

- Runtime README / deployment contract gain `/v1/commands`, `/v1/runs/:id`,
  `/v1/approvals/:id/decision`, `/v1/executions…` as the governed ingress.
- Week 1 Mission 001 work continues: Service Catalog, Grok adapter, and
  end-to-end revenue proof on these primitives.
- A second “gateway” microservice is an explicit non-goal.

## References

- Notion: AION Execution Platform — Progress Assessment (Sep 2026)
- Notion: Verdict / Next best steps (Sep 2026)
- ADR-001 (greenfield reset), ADR-002 (runtime host ownership)
- Linear AIO-9 / AIO-11 (legacy gateway reconciliation intent)
