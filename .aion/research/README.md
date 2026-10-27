# Research Desk (AION — RESEARCH)

Isolated research notes, comparison matrices, and PoC pointers owned by
[Research / Prototype Engineer](../agents/12-research-engineer.md).

## Principle

**Research first → Prototype second → Production integration only after evidence.**

Nothing here is production-ready by presence. Promotion requires the
[production-readiness checklist](../../engineering/production-readiness.md), an
Architect go/no-go, and usually an [ADR](../../adr/README.md).

## Layout

| Path | Purpose |
|---|---|
| [capability-backlog.md](capability-backlog.md) | Ranked research candidates (evidence + isolation) |
| [production-readiness-2026-10-12.md](production-readiness-2026-10-12.md) | **Current** dated production readiness assessment → certify `20fe3a4` GE-001 candidate |
| [../audits/agentic-engineering-audit-2026-10-26.md](../audits/agentic-engineering-audit-2026-10-26.md) | **2026-10-26** Agentic Engineering audit — near/out-of-loop autonomy vs AION governance |
| [production-readiness-2026-09-28.md](production-readiness-2026-09-28.md) | Prior assessment (2026-09-28) → GOVERNED EXECUTION |
| [techops-monday-brief-2026-10-05.md](techops-monday-brief-2026-10-05.md) | TechOps Monday Brief — agent identity as infrastructure |
| [techops-monday-brief-2026-10-26.md](techops-monday-brief-2026-10-26.md) | TechOps Monday Intelligence Brief — supply-chain, PHP, CA trust, ransomware, financial agents |
| [../audits/aion-exposure-verification-2026-10-26.md](../audits/aion-exposure-verification-2026-10-26.md) | Customer-zero exposure verification from the Oct 26 brief |
| [dsl-001-data-licensing-market-brief-2026-10-20.md](dsl-001-data-licensing-market-brief-2026-10-20.md) | Enterprise operational-data licensing market brief → P-AIO-9 |
| `RES-NNN-*.md` | Per-mission research briefs / comparison matrices |
| Prototypes | Live **outside** `main` (feature branches / sandboxes); link from the brief |

## Hard rules

- No production credentials in spikes.
- Do not merge experimental vendor SDKs into `aion-core` or `aion-runtime` without ADR.
- Runtime stays provider-SDK-free ([ADR-002](../../adr/ADR-002-runtime-host-ownership.md)).
- Platformize only under the four conditions in
  [platform maturity](../../roadmap/platform-maturity.md).
- Autonomy for research notes is **L0–L1** (observe / recommend) unless a
  named mission says otherwise.

## Handoff

Finish every slice with the
[agent handoff](../standards/agent-handoff.md) block. Architect + Orchestrator
decide promotion; Research does not silently land production dependencies.
