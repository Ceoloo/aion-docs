# Agent Missions

Product/commercial missions (problem, user, outcome, non-goals) remain in
[`../../missions/`](../../missions/).

This folder is the **Cursor execution overlay**: how the engineering team
assigns ownership, branches, and acceptance for implementation work.

## Files

| File | Purpose |
|---|---|
| [TEMPLATE.md](TEMPLATE.md) | Copy for each agent-owned implementation mission |
| [RES-001-model-provider-contract.md](RES-001-model-provider-contract.md) | RESEARCH — model-provider contract (research + prototype) |
| [RES-002-friday-production-brief.md](RES-002-friday-production-brief.md) | RESEARCH — industry brief → GOVERNED EXECUTION certify-first |
| [GE-001-governed-execution-certify.md](GE-001-governed-execution-certify.md) | EXECUTION — certify one clean GOVERNED EXECUTION workflow (2026-09-28) |
| [SOV-001-sovereignty-audit.md](SOV-001-sovereignty-audit.md) | P0 — Phase 1 sovereignty audit (ADR-016); inventory + classify exposures |
| [SOV-002-local-inference-model-router.md](SOV-002-local-inference-model-router.md) | P0 — Local inference + Sovereign Model Router (ADR-017); contracted |
| [SOV-003-restricted-external-gateway.md](SOV-003-restricted-external-gateway.md) | P0 — Restricted External Gateway IP policy (ADR-018); contracted |
| [CRM-001-twenty-integration-benchmark.md](CRM-001-twenty-integration-benchmark.md) | CRM — Twenty Integration & Benchmark (ADR-019); reuse-first, no new microservice |
| This README | How agent missions relate to product missions |

## Rules

1. One **primary owner** per mission.
2. Supporting agents assist; they do not silently co-own the same files.
3. Reviewers (QA / Security / Architect) are named up front.
4. Link a product mission ID when the work ships user-facing capability.
5. Status lives in the mission file and/or PR — not only in chat.

## Lifecycle (agent mission)

```
DEFINED → OWNED → IN PROGRESS → HANDOFF/REVIEW → MERGED → PROVEN → CLOSED
```

Blocked work must create a [handoff](../handoffs/TEMPLATE.md) naming the
blocking contract.
