# Handoffs

Agents communicate through artifacts. When one specialist depends on another,
create a handoff file (or paste the block into both chats / the PR).

The machine contract is **`AgentHandoff`** in `@aion/core`
(`facts`, `uncertainties`, `artifactRefs`, `confidence`). Markdown templates
here are a human rendering of that schema — do not invent a parallel shape.

## Files

| File | Purpose |
|---|---|
| [TEMPLATE.md](TEMPLATE.md) | Dependency / delegation handoff (+ JSON) |
| [example-agent-handoff.json](example-agent-handoff.json) | Valid schemaVersion 1 example |
| [2026-09-12-docs-lag-sync.md](2026-09-12-docs-lag-sync.md) | Docs maturity / ownership honesty sync |
| [2026-09-12-docs-to-architect-desks-adr.md](2026-09-12-docs-to-architect-desks-adr.md) | DOCS → ARCHITECT: ratify ADR-004 desks ownership |
| [`../standards/agent-handoff.md`](../standards/agent-handoff.md) | Completion + A2A standard |

## Rules

1. Prefer a written **CONTRACT** (fields, statuses, API shapes) over chat prose.
2. Always include **facts / uncertainties / artifactRefs / confidence**.
3. Mark **BLOCKING: Yes** when the consumer cannot proceed without it.
4. Orchestrator tracks open blocking handoffs and merge order.
5. Close the handoff when the contract lands (PR merged or schema shipped) and
   the consumer confirms.
6. Never redefine `AgentHandoff` in Runtime or Products — import from Core.
