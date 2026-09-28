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
