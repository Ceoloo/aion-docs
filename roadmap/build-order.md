# Build Order

AION is assembled in phases. The order is deliberate: **architecture before
contracts, contracts before data, data before infrastructure, and infrastructure
before products** — with instrumentation and learning layered on once there is
something real to instrument and learn from.

```mermaid
flowchart TD
    P0["PHASE 0 — Architecture<br/>aion-docs"] --> P1["PHASE 1 — Core contracts<br/>aion-core"]
    P1 --> P2["PHASE 2 — Canonical data foundation<br/>aion-data"]
    P2 --> P3["PHASE 3 — Minimum infrastructure required<br/>aion-infra"]
    P3 --> P4["PHASE 4 — First production mission/product<br/>aion-products"]
    P4 --> P5["PHASE 5 — Outcome instrumentation"]
    P5 --> P6["PHASE 6 — Learning loops"]
    P6 --> P7["PHASE 7 — Platformization of repeated capabilities"]
```

| Phase | Focus | Repo(s) |
|---|---|---|
| **0** | Architecture & governance — the constitution. **(current)** | aion-docs |
| **1** | Core contracts — orchestration, execution, agent/tool interfaces, permissions. | aion-core |
| **2** | Canonical data foundation — schemas, events, the six data kinds. | aion-data |
| **3** | Minimum infrastructure required — only what current missions need. | aion-infra |
| **4** | First production mission/product. | aion-products |
| **5** | Outcome instrumentation — record real outcomes. | aion-data + aion-core |
| **6** | Learning loops — turn outcomes into lessons and memory. | aion-data + aion-core |
| **7** | Platformization — promote repeated capabilities, under the rule. | aion-core |

## The critical caveat

**A phase does NOT mean every imagined subsystem gets implemented during it.**
Each phase implements **only what is required to support current missions**
([Mission Before Infrastructure](../engineering/principles.md)).

- Phase 1 does not build the entire Company OS — it builds the contracts the
  first mission needs.
- Phase 2 does not model every business entity — it models the entities the
  first mission needs.
- Phase 3 builds the **minimum** infrastructure, not a speculative platform.
- Phases 5–7 begin only once there is real product activity producing outcomes
  worth instrumenting and learning from.

## Why this order

- **Docs first** so that where code belongs is decided before code exists — the
  direct counter to the [reset](../adr/ADR-001-greenfield-reset.md).
- **Core contracts before data implementation** so data serves defined
  execution needs, not the reverse.
- **Data before infra** so infrastructure is provisioned against real data needs,
  not guessed ones.
- **A real product before instrumentation** so outcomes and learning have
  something true to measure.

## Current position

AION is in **Phase 0**. `aion-docs` (this repository) is being established. **Do
not begin implementing `aion-core`, `aion-data`, `aion-infra`, or
`aion-products` yet.** The recommended next engineering action is defined at the
close of Phase 0 — see the root [README](../README.md) and
[../repositories/aion-core.md](../repositories/aion-core.md).

## Invariants

- **Phases are sequenced, but scope within a phase is mission-bounded.**
- **No speculative subsystems.**
- **Instrumentation and learning follow real product activity.**
