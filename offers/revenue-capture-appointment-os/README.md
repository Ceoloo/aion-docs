# Revenue Capture & Appointment OS — Sprint pack

**Offer:** flagship under [Outcome-Based Services Blueprint](../aion-outcome-based-services-blueprint.md)  
**ADR:** [ADR-014](../../adr/ADR-014-outcome-based-systems-engineering.md) (Accepted)  
**Mission:** [MISSION-RCAOS-001](../../missions/MISSION-RCAOS-001-revenue-capture-appointment-os.md)

## Sprint 1 lock (Oct 12–13) — commercial definition + architecture

| Artifact | Status | Document |
|---|---|---|
| Process contract (template + demo instance) | **Locked** | [process-contract.md](process-contract.md) |
| Required GHL capabilities | **Locked** | [ghl-capabilities.md](ghl-capabilities.md) |
| Baseline method | **Locked** | [baseline-method.md](baseline-method.md) |
| Demo-client workflow design | **Locked** | [demo-client-workflow.md](demo-client-workflow.md) |
| Acceptance tests | **Locked** (in process contract) | [process-contract.md](process-contract.md#acceptance-tests) |

These locks define **what we will build and measure**. They are **not** production
rollout or pilot-pass evidence. Sprint 2 (Oct 19–20) implements and proves the
path under permissions and operational acceptance.

## Aligns with existing platform work

- Secure Automation workflow `lead-to-appointment-v1` in `@aion/core`
- Governed GHL path: [roadmap/ghl-readonly-governed-write.md](../../roadmap/ghl-readonly-governed-write.md)
- AIO-17 enabled CRM slice: Runtime `docs/ghl-aio17-lead-workflow.md`
- OL-001 revenue production loop (separate cohort accounting)

## Explicit honesty

| Claim | Truth |
|---|---|
| Offer + process defined | Yes (this pack) |
| Appointment **write** via gateway certified | No — human books; `crm.appointment.create` inactive |
| Autonomous customer messaging | No — draft only; `crm.message.send` deferred |
| External SMB pilot live | No — Sprint 2+ |
