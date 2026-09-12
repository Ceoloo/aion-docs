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
