# Capability research backlog

Ranked by **OL value × evidence gap × isolation safety**. Updated by AION —
RESEARCH. Not a commit to build — a queue to investigate.

| Rank | Candidate | Evidence today | Isolation | Recommend |
|---|---|---|---|---|
| **1** | **Platform model-provider contract** ([AIO-16](https://linear.app/aion-empire/issue/AIO-16/p0-implement-model-provider-integration-contract)) | Product-local `LlmProvider` (Anthropic + OpenRouter) exists; Runtime PRE-OL shows ~3 ms stub latency and **no live model proof**; M007 recommend-only ranking has no live generation adapter | Contract + telemetry spike in products / research branch; keep SDKs out of Runtime | **Active research** → see [RES-001](RES-001-model-provider-contract.md) |
| **2** | **GHL adapter contract fidelity** ([AIO-17](https://linear.app/aion-empire/issue/AIO-17/p0-implement-gohighlevel-adapter-contract)) | M009 live plane landed; Integrations owns adapter surface | Fixture-first; no write to live CRM without gate | Hand to **Integrations**; Research only if vendor API shape is still ambiguous |
| **3** | **Learning loop / memory kinds** ([AIO-15](https://linear.app/aion-empire/issue/AIO-15/implement-aion-governance-memory-learning-observability-and-economic)) | Six data kinds defined; Phase 6 of build order requires real product outcomes first | Docs + schema spikes only until OL produces outcomes | **Defer prototype** until OL-001 yields durable outcomes worth learning from |
| **4** | **Workforce migration pattern** (AIO-12 / AIO-13) | Legacy products exist outside six-repo canon; greenfield reset forbids silent import | Paper migration map + one thin capability registration PoC | Research **pattern** only; Product/Architect own migration missions |
| **5** | **Adaptive / bandit routing** | Explicit **non-goal** of Mission 007; deterministic fallback mandatory | Would require new mission + eval harness | **Kill until** scorecards have live model I/O and sample depth |
| **6** | **Frontier / experimental compute** | Portfolio allocates ≤10%; Assets/Frontier stubbed until M001/M002 proof domains earned | `/06-frontier` budget-capped | **Out of scope** for Systems P0 research |

## Scoring rubric (for new entries)

1. Does an active mission or OL dependency require it?
2. Is there already a contract/ADR covering it? (If yes → stop; implement, don't re-research.)
3. Can a PoC stay off the Runtime image and off Core imports of vendor SDKs?
4. What evidence would kill vs promote the idea?

## Current focus

**RES-001 — Model-provider integration contract** (research → recommendation →
optional isolated prototype). No production merge until Architect accepts the
evidence pack.
