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
6. pass the sovereignty gate when critical (or be classified optional external)

Doctrine: [`../../architecture/sovereign-intelligence-doctrine.md`](../../architecture/sovereign-intelligence-doctrine.md)
([ADR-016](../../adr/ADR-016-sovereign-intelligence-doctrine.md)).

## Current company priority

Reliable execution of real business workflows > cosmetic surfaces.
Favor revenue, execution durability, reliability, observability, reuse,
client deployment speed, **and sovereign control of data/intelligence/execution**.
Program: [`../../roadmap/sovereignty-program.md`](../../roadmap/sovereignty-program.md).
