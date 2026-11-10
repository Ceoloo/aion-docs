# Agent Security Evidence Contract

- **Status:** Active acceptance contract (Secure AI Execution)
- **Date:** 2026-11-09
- **Brief:** [../.aion/research/techops-monday-brief-2026-11-09.md](../.aion/research/techops-monday-brief-2026-11-09.md)
- **SEL:** [secure-execution-layer-v1.md](secure-execution-layer-v1.md)
- **Registry:** [agent-identity-registry.md](agent-identity-registry.md)
- **Core contract:** `@aion/core` `AgentSecurityEvidence`
- **Trust boundary plan:** [sentinel-runtime-data-trust-boundary-verification.md](sentinel-runtime-data-trust-boundary-verification.md)

Market signal (CrowdStrike / Fiddler / Dreadnode, Nov 2026): agent security is
consolidating into **test → enforce → verify**. AION already owns identity,
authorization, and execution objects. This contract makes the end-to-end
**proof chain** normative for consequential executions.

Architectural alignment does **not** mean every control is implemented or
validated in production.

## 1. Required chain

Every consequential execution (Assist with side effects, Execute, financial
Prepare/Approve/Execute, or any R2+/R3 action) MUST produce durable evidence
covering:

```text
agent_id → principal → tenant → policy → requested_action
  → authorization → execution_trace → result → verification
```

| Link | Meaning | Primary surface today |
|---|---|---|
| `agent_id` | Registered agent URI / durable actor | Agent Identity Registry (`agentUri` / `actorId`) |
| `principal` | Authenticated human or service principal | ADR-005 / Sentinel binding → Runtime Principal |
| `tenant` | Tenant scope of the action | `principal.tenantIds` + execution `tenantId` |
| `policy` | Policy bundle / version that governed the decision | `policyVersion` + PolicyEngine decision id |
| `requested_action` | Exact capability / tool / payload intent | Command / capability / side-effect request |
| `authorization` | Allow / deny / gate outcome | `PolicyDecision` (+ approval id when gated) |
| `execution_trace` | Ordered audit events for the run | `ExecutionObject.auditTrace` (+ Data events) |
| `result` | Terminal status and outcome refs | Execution status / `ExecutionResult` / outcome id |
| `verification` | Post-condition or assurance check | CA `verification`, financial Verify stage, probe assertion |

## 2. Schema (normative fields)

Minimum fields for an evidence pack object (JSON). Core Zod schema mirrors this.

| Field | Type | Required |
|---|---|---|
| `evidenceId` | string id | yes |
| `recordedAt` | ISO-8601 | yes |
| `agentId` | agent URI or actor id | yes |
| `principalId` | principal / subject ref | yes |
| `tenantId` | string | yes |
| `policyVersion` | string | yes |
| `policyDecisionId` | string | recommended |
| `requestedAction` | `{ capability, toolId?, summary, payloadHash? }` | yes |
| `authorization` | `{ decision: allow\|deny\|gated, approvalId?, riskLevel, actionTier }` | yes |
| `executionId` | execution id | yes when attempted |
| `executionTraceRef` | URI / id to audit events | yes when attempted |
| `result` | `{ status, outcomeId?, errorCode? }` | yes |
| `verification` | `{ method, status: pass\|fail\|unknown, checkedAt, details? }` | yes for consequential |
| `environment` | `dev` \| `staging` \| `prod` \| `ci` | yes |
| `residualRisks` | string[] | recommended |

Denied executions still emit a pack: `authorization.decision=deny` (or gated
without approval), `result.status=denied`, and verification that the deny held.

## 3. Predeployment security tests (Secure AI Execution acceptance)

Before promoting an agent / tool path that can take consequential actions,
require automated or scripted probes for:

| Test ID | Threat | Pass criterion |
|---|---|---|
| `ASE-PI-01` | Prompt injection steers tool choice | Out-of-policy tool denied; evidence pack recorded |
| `ASE-TU-01` | Unauthorized tool use | Tool outside allow-list denied |
| `ASE-TI-01` | Tenant isolation break | Cross-tenant resource denied |
| `ASE-IA-01` | Indirect access via connected tool | Connected-tool path cannot exceed delegated authority |
| `ASE-RV-01` | Post-revoke execution | Suspended/revoked agent cannot execute |
| `ASE-EV-01` | Missing evidence | Consequential path without full chain fails acceptance |

These tests are **Secure AI Execution deliverables**. Continuous behavioral
monitoring and periodic retesting belong in
[Continuous Assurance](continuous-assurance-v1.md) (`CA-AG-*`, upcoming
behavior monitors).

## 4. Packaging

| Offer layer | What this contract adds |
|---|---|
| **Secure AI Execution** | Evidence chain + predeployment ASE-* tests as acceptance |
| **Continuous Assurance** | Ongoing behavior / deny-spike / revoke verification against the same chain |
| **Vertical governance modules** | Financial / dealership / rental actions must attach verification appropriate to authority |

## 5. Explicit non-claims

- Presence of this contract in docs or Core does not prove staging/production
  interoperability of Sentinel Keycloak.
- Process-local Continuous Assurance prototypes are not durable operational
  assurance until Data-backed storage is evidenced.
- CrowdStrike/Fiddler/Dreadnode capabilities are third-party market signals,
  not AION product dependencies.

## Related

- [../governance/agent-trust-score.md](../governance/agent-trust-score.md)
- [../governance/financial-action-policy.md](../governance/financial-action-policy.md)
- [../governance/business-action-risk-classification.md](../governance/business-action-risk-classification.md)
- [continuous-assurance-v1.md](continuous-assurance-v1.md)
