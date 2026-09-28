# Capability research backlog

Ranked by **OL value × evidence gap × isolation safety**. Updated by AION —
RESEARCH. Not a commit to build — a queue to investigate.

| Rank | Candidate | Evidence today | Isolation | Recommend |
|---|---|---|---|---|
| **0** | **GOVERNED EXECUTION acceptance upgrade** ([RES-002](RES-002-friday-production-brief.md), [2026-09-28 readiness](production-readiness-2026-09-28.md), [GE-001](../missions/GE-001-governed-execution-certify.md)) | Live CRM write demonstrated; OL-001 contaminated (synthetic→Annfiera); Runtime #57 on main (`20fe3a4`) not evidenced as serving image; Products `main` red (#44 green); last prod digest Sep 22 `10de0663` | Evidence discipline + pinned release + clean live mission; no new subsystem | **Active — certify first (blocked on #44 merge + pinned deploy)** |
| **1** | **Platform model-provider contract** ([AIO-16](https://linear.app/aion-empire/issue/AIO-16/p0-implement-model-provider-integration-contract)) | Product-local `LlmProvider` + RES-001 telemetry spike landed (keyless); Runtime PRE-OL still lacks live model proof; M007 recommend-only | Contract + telemetry in products; keep SDKs out of Runtime | **Prototype complete** — awaiting Architect ADR before Core types; see [RES-001](RES-001-model-provider-contract.md) |
| **2** | **Mission Authority Envelope** (compose DelegatedAuthority + mission limits) | Anticipatory-oversight industry signal; R0–R3 + M008 + approvals exist; no first-class mission envelope on create | Design/ADR only until Operator Loop live PASS | **P0 design after GOVERNED EXECUTION** |
| **3** | **MissionOutcome 4D rollup** (execution/quality/governance/economics) | Snowflake-style quality+spend signal; pieces exist separately | Compose view over existing tables | **P0 schema compose; P1 UI** |
| **4** | **GHL adapter contract fidelity** ([AIO-17](https://linear.app/aion-empire/issue/AIO-17/p0-implement-gohighlevel-adapter-contract)) | M009 live plane landed; Integrations owns adapter surface | Fixture-first; no write to live CRM without gate | Hand to **Integrations** |
| **5** | **`contain(agent_id)` reversible containment** | Okta Blueprint signal; FeatureGate kill-switch + autonomy demote only | Design note; preserve runtime state | **P1 design — not this week** |
| **6** | **OTLP export seam** (CloudWatch Omni-class backends) | AWS Omni validates evals-as-telemetry; AION spine is business-ID not OTEL spans | Infra ADR + exporter only; never vendor SDK in Core/Runtime | **Defer until GOVERNED EXECUTION PASS + ADR** |
| **7** | **Skills registry / SkillManifest** | GitHub workflow study; Skills conceptual in AI engineer card only | Would fail platformization (one consumer); Workflow + Service Catalog suffice | **Kill until second product consumer** |
| **8** | **Learning loop / memory kinds** ([AIO-15](https://linear.app/aion-empire/issue/AIO-15/implement-aion-governance-memory-learning-observability-and-economic)) | Six data kinds defined; Phase 6 needs real outcomes | Docs + schema spikes only | **Defer** until OL produces durable outcomes |
| **9** | **Workforce migration pattern** (AIO-12 / AIO-13) | Legacy products exist outside six-repo canon; greenfield reset forbids silent import | Paper migration map + one thin capability registration PoC | Research **pattern** only; Product/Architect own migration missions |
| **10** | **Adaptive / bandit routing** | Explicit **non-goal** of Mission 007 | Would require new mission + eval harness | **Kill until** live model I/O sample depth |
| **11** | **Frontier / experimental compute** | Portfolio ≤10%; stubs until proof domains earned | `/06-frontier` budget-capped | **Out of scope** for Systems P0 |

## Scoring rubric (for new entries)

1. Does an active mission or OL dependency require it?
2. Is there already a contract/ADR covering it? (If yes → stop; implement, don't re-research.)
3. Can a PoC stay off the Runtime image and off Core imports of vendor SDKs?
4. What evidence would kill vs promote the idea?

## Current focus

**GE-001 / RES-002** — Certify-first: raise Operator Loop live acceptance to **GOVERNED EXECUTION — VERIFIED**.  
Current position (2026-09-28): live capability demonstrated; clean certification open; Products #44 + Runtime #57 deploy are the immediate gates. See [production-readiness-2026-09-28](production-readiness-2026-09-28.md) and [governed-execution-certify](../../roadmap/governed-execution-certify.md).  
**RES-001** — Products telemetry prototype complete; Core types still ADR-gated.
