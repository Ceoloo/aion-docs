# Handoff — Frontend workflow polish (UX-001)

```text
HANDOFF ID: 2026-09-12-frontend-workflow-polish
NEED:       Operator/product UI workflow polish across consoles + desks
FROM:       Frontend / UX Engineer
FOR:        Product Engineer, QA Engineer, Documentation Engineer
CONTRACT:
  - Command Center answers “what needs attention?” before aggregate KPIs
  - Loading / error / empty states are shared primitives (no invented KPIs)
  - Approvals reachable from mission + execution detail (decide in context)
  - Mobile operator navigation (header menu + sticky quick nav)
  - Desks checkout: busy + error surfaced; Payment Link fallback when Price IDs unset
  - Revenue Copilot LaunchPad: health failure is visible/actionable (not silent null)
BLOCKING?:  No
CONTEXT:    Operating leverage UX-001 — Command Center → act → Runtime.
            Principle: expose system state, decisions, outcomes — not decorative dashboards.
DUE / PRIORITY: Now / P1 for OL-001 operator friction
STATUS: open
DELIVERED IN:
  - aion-products branch cursor/workflow-polish-operator-ux-53e8
  - aion-desks branch cursor/workflow-polish-desks-53e8
```

## Acceptance

- [ ] Holding Overview shows Needs attention rail from real approvals/failures
- [ ] Empty mission lists offer Launch CTA (no fake metrics)
- [ ] Mission/Execution detail can open Approval Panel for in-context decide
- [ ] Mobile: Command / OL-001 / Missions / Launch reachable without desktop nav
- [ ] Desks BuyButton shows “Starting checkout…” and actionable error if both Price ID and Payment Link fail
- [ ] Copilot readiness strip shows failure/loading instead of disappearing

## Follow-ups (not in this slice)

- Policy-aware retry UI (deferred until Runtime exposes eligibility)
- Shared design-tokens package across products/desks (optional)
- Visual regression screenshots once preview deploy is available
