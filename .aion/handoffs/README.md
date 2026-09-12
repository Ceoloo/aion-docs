# Handoffs

Agents communicate through artifacts. When one specialist depends on another,
create a handoff file (or paste the block into both chats / the PR).

## Files

| File | Purpose |
|---|---|
| [TEMPLATE.md](TEMPLATE.md) | Dependency / contract request |
| [`../standards/agent-handoff.md`](../standards/agent-handoff.md) | End-of-mission completion format |

## Rules

1. Prefer a written **CONTRACT** (fields, statuses, API shapes) over chat prose.
2. Mark **BLOCKING: Yes** when the consumer cannot proceed without it.
3. Orchestrator tracks open blocking handoffs and merge order.
4. Close the handoff when the contract lands (PR merged or schema shipped) and
   the consumer confirms.
