# Handoff Template

Copy to `.aion/handoffs/<from>-to-<for>-<slug>.md` or paste into mission/PR.

---

```markdown
HANDOFF ID:
NEED:
FROM:
FOR:
CONTRACT:
BLOCKING?:
CONTEXT:
DUE / PRIORITY:
STATUS:                 (open | delivered | accepted | cancelled)
DELIVERED IN:           (PR / commit / path)
```

## Example

```text
NEED:     Canonical ExecutionRecord schema
FROM:     Data Engineer
FOR:      Runtime Engineer
CONTRACT:
  - execution_id
  - workflow_id
  - status
  - started_at
  - completed_at
  - cost
  - outcome
BLOCKING?: Yes
```

## Acceptance

The **FOR** agent accepts when they can implement against the contract without
guessing field names, nullability, or ownership.
