# Architect review — RES-001 Model-provider contract

**Reviewer:** AION — ARCHITECT  
**Date:** 2026-09-12  
**Subject:** [RES-001 research brief](../research/RES-001-model-provider-contract.md)  
**Decision:** **CONDITIONAL GO**

---

## Decision

| Slice | Verdict | Notes |
|---|---|---|
| Products telemetry spike (`generate` + required telemetry; no stream/tool) | **GO** | Isolated in `aion-products`; keyless CI; no Runtime/Core SDK imports |
| Narrow Core types contract (`model.generate@1`) | **GO IN PRINCIPLE** | Requires a short ADR before Core lands types; **not** in this spike |
| Vendor SDKs in Core or Runtime | **NO-GO** | Violates [ADR-002](../../adr/ADR-002-runtime-host-ownership.md) |
| Adaptive / auto provider switching | **NO-GO** | Remains M007 non-goal until live sample depth exists |
| Gateway proxy (LiteLLM / Bedrock / etc.) | **DEFER** | Premature infra; revisit only with multi-product + ops evidence |

---

## Why GO (products spike)

1. **Evidence gap is real:** PRE-OL lacks live model proof; product `LlmProvider` already exists and is the right isolation boundary.
2. **Platformization rule:** Only one product consumer today → adapters stay in products ([platform maturity](../../roadmap/platform-maturity.md)).
3. **Boundary safety:** Spike does not touch Runtime image or Core imports of provider SDKs.
4. **Kill criteria from RES-001 are accepted** as merge gates for any follow-on PR.

## Conditions (must hold)

1. No provider SDK added to `aion-core` or `aion-runtime`.
2. CI remains keyless (fake `fetch` / scripted provider).
3. MVP surface = text `generate`/`complete` + telemetry fields only — **no** `stream` / `tool_call` in this spike.
4. No adaptive routing wiring.
5. Hardcoded provider labels in execution metadata must use `llm.name`, not a vendor string.
6. Before promoting types into Core: draft ADR + show a second consumer **or** an explicit OL-001 Runtime capability registration need.

## Ownership

| Work | Owner |
|---|---|
| Telemetry-complete products spike | RESEARCH (implement) → AI/Product (review) |
| Future Core contract + ADR | Architect authors ADR; AI Engineer implements types |
| Runtime capability registration | Runtime — only after Core contract ADR accepted |

## Next

RESEARCH executed the isolated products spike (`aion-products`
`cursor/res001-model-telemetry-spike-da87`). RES-001 status →
`prototype-complete` with keyless proof. Remaining: Architect ADR before any
Core types; no Runtime SDK imports.
