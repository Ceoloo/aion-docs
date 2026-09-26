# aion-action-engine

**Role:** Operational module — the **Action Queue** (Signal → Recommendation →
Action → Approval → Execution → Outcome) and the host of the **Decision Plane**
package `@aion/decision-engine` ([ADR-009](../adr/ADR-009-decision-plane.md)),
with PostHog shadow analytics ([ADR-010](../adr/ADR-010-posthog-analytics-plane.md)).

> **Canon note:** like [`aion-desks`](aion-desks.md), this is a **workspace
> satellite**, not one of the six canonical platform repositories. It must not
> become a parallel control plane.

## Owns

- the Action Object lifecycle and operator queue UI (`apps/web`)
- producer connectors that turn signals (Revenue Copilot, agents) into actions
- Context Pack hydration before execution
- `@aion/decision-engine`: typed, calibrated judgments + shadow evaluation
- `@aion/action-core`: workspace-local tool/permission types
  (renamed from `@aion/core` on 2026-09-26 — that name belongs to the canonical
  kernel)

## Does NOT own

- authorization of side effects — the `PolicyEngine` + Runtime Execution
  Gateway are the sole authority (ADR-003, ADR-008, ADR-009 §5)
- canonical entities (leads, outcomes, executions) — those live in `aion-data`
- infrastructure or deployment profiles (`aion-infra`)

## Integration status (2026-09-26 audit)

| Seam | Today | Target |
|---|---|---|
| Persistence | Local SQLite (`services/action-service`) | Queue state may stay local; **outcomes** and executed actions must be recorded canonically via the Runtime (`aion_execution` + outcome) |
| Execution | `POST /v1/actions/:id/execute` only transitions queue status + builds a Context Pack; no external side effects | Dispatch governed work as `POST /v1/commands` (`serviceKey`) to the Runtime Execution Gateway; store the returned `executionId` on the Action Object |
| Approval | Own approve/claim with header roles (`x-aion-role`) | Human gates for governed commands are the Runtime's `POST /v1/approvals/:id/decision`; queue approval is a UX pre-step, not authority |
| Deployment | Not deployed by `aion-infra` | Add a profile only once a mission requires it |

## Related

- [dependency-rules.md](dependency-rules.md)
- [../architecture/system-integration-overview.md](../architecture/system-integration-overview.md)
