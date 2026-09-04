# Architecture

This section is the load-bearing description of the AION system: what the
layers are, what each is responsible for, and where the seams between them run.
It defines architecture and contracts — not implementation.

## Documents

| Document | Purpose |
|---|---|
| [system-overview.md](system-overview.md) | What AION is and is not; the layered model. |
| [system-context.md](system-context.md) | AION's boundary — external actors and systems. |
| [logical-architecture.md](logical-architecture.md) | The layers and how requests flow through them. |
| [control-plane.md](control-plane.md) | Orchestration, routing, policy, human gates. |
| [execution-layer.md](execution-layer.md) | Agents, workers, tools, external runtimes, humans. |
| [execution-gateway.md](execution-gateway.md) | The boundary enforcing at-most-once, replay-safe, evidenced execution. |
| [intelligence-layer.md](intelligence-layer.md) | Context, memory, reasoning, learning, evals. |
| [data-layer.md](data-layer.md) | Canonical data, events, memory, lessons, analytics. |
| [product-layer.md](product-layer.md) | How products consume the platform. |
| [learning-loop.md](learning-loop.md) | Context → outcome → lesson → memory. |
| [observability.md](observability.md) | The traceability model for every action. |
| [agent-economics.md](agent-economics.md) | Cost → completion → value → ROI as a first-class layer. |
| [security-model.md](security-model.md) | Identities, least privilege, controls. |
| [environments.md](environments.md) | Environment isolation and promotion. |

## Reading order

Start with `system-overview.md`, then `logical-architecture.md`. The layer
documents (`control-plane`, `execution-layer`, `intelligence-layer`,
`data-layer`, `product-layer`) can then be read in any order.
`learning-loop.md`, `observability.md`, and `security-model.md` are
cross-cutting and apply to every layer.

## Non-goals of this section

- No vendor lock-in. When a technology is not chosen, the **required
  capability** is documented and the choice deferred to an ADR.
- No implementation detail that belongs in a code repository.
- No speculative subsystems. A layer being described here does not mean it is
  built now; see [../roadmap/build-order.md](../roadmap/build-order.md).
