# Operator UI workflow standard

Applies to `aion-products/workforce-control`, `aion-products/web`, and `aion-desks`.

## Principle

Interfaces expose **system state, decisions, and outcomes**. Never invent KPIs,
success rates, or readiness when APIs are empty or failing.

## Attention loop

```text
Open surface
  → What needs attention? (approvals, failures, blockers)
  → Act (approve / launch / inspect / buy)
  → Authority stays in Runtime / Stripe / product API
```

Command Center and LaunchPad lead with attention/readiness — not ornamental metric grids.

## Required states

Every data-backed view must implement:

| State | Rule |
|---|---|
| Loading | Explicit status text; no skeleton fake numbers |
| Error | Show API/Runtime message + retry when safe |
| Empty | Honest empty copy + next action when one exists |
| Success / outcome | Persist visible terminal outcome (no silent waiver) |

## Writes

UI buttons only call governed APIs (Runtime approvals/missions, Stripe checkout).
The UI is never the business-logic layer.

## Responsive

Operator console must expose primary destinations on mobile (Command, scoreboard,
missions, launch). Do not leave navigation desktop-only.
