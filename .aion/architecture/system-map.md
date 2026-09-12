# System Map (Cursor orientation)

Authoritative deep dive: [`../../architecture/`](../../architecture/).

## Control flow

```
MISSION / REQUEST
      ↓
COMMAND (+ Actor)
      ↓
CONTEXT / VALIDATION
      ↓
POLICY + PERMISSIONS  → DENY
      ↓
RISK (R0–R3)
      ↓
APPROVAL GATE         → awaiting_approval → human decide
      ↓
EXECUTION ROUTING     (capability / serviceKey)
      ↓
EXECUTION ADAPTER / RUNTIME WORKER
      ↓
RESULT (normalized)
      ↓
EVENTS + TELEMETRY + EXECUTION RECORD
      ↓
OUTCOME (≠ result)
```

## Layer → repo

| Layer | Repo |
|---|---|
| Governance / constitution | `aion-docs` |
| Company OS + control plane | `aion-core` |
| Canonical durable truth | `aion-data` |
| Composition root + Execution Gateway | `aion-runtime` |
| Environments / deploy / secrets | `aion-infra` |
| Products (Revenue Copilot, Workforce) | `aion-products` |
| Desk commerce landing | `aion-desks` (workspace present; not in six-repo canon yet) |

## What “done” looks like for platform work

A real AION workflow should:

1. survive process restart
2. leave an auditable execution record
3. respect permissions and approval gates
4. emit observable events / telemetry
5. connect to measurable business outcome where applicable

## Current company priority

Reliable execution of real business workflows > cosmetic surfaces.
Favor revenue, execution durability, reliability, observability, reuse, and
client deployment speed.
