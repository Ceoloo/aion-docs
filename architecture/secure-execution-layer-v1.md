# Secure Execution Layer v1

- **Status:** Active (AIO-45)
- **Date:** 2026-10-06
- **ADR:** [ADR-012](../adr/ADR-012-secure-execution-layer-v1.md)
- **Standard map:** SIS-GX-01…10 in
  [security-infrastructure-standard-v1.md](security-infrastructure-standard-v1.md) §3.10
- **Gateway ownership:** [ADR-003](../adr/ADR-003-execution-gateway-into-runtime.md)

This document is the **product contract** for AION’s agent gateway / control
plane: what must be true on every sensitive execution path, how components
interface, and how today’s Runtime gateway migrates to full SEL posture.

## 1. Hard rule

```text
Agent → AION Gateway (Runtime) → Policy (Core) → Adapter / Business API
```

Never `agent → raw LoB API key → business API`. Credentials are vaulted,
short-lived, or delegated where the provider supports it (SIS-GX-07).

## 2. Required controls

| Control | SIS | Runtime / Core surface |
|---|---|---|
| Authenticated principal before authorization | SIS-GX-01 | ADR-005 identity plane → `authenticateRequest` |
| Tenant binding | SIS-GX-02 | `x-aion-tenant-id` + Principal.tenantIds; PolicyEngine tenant-scope |
| Tool allow-lists | SIS-GX-03 | `AgentActor.allowedTools` + policy tool check |
| Observe / Assist / Execute | SIS-GX-04 | ADR-007 + PolicyEngine action-tier |
| Data-scope enforcement | SIS-GX-05 | `allowedData` + `resourceDataClasses` |
| Human approval for sensitive actions | SIS-GX-06 | R3 always; gated R2; ApprovalGate |
| Short-lived / vaulted credentials | SIS-GX-07 | Adapter credential providers; no keys in agent runtime |
| Full execution audit evidence | SIS-GX-08 | `executions` + events + Trust Score |
| Rate / spend limits | SIS-GX-09 (R) | Cost on Execution Object; budget checks; catalog cost hints |
| Emergency revoke + kill / contain | SIS-GX-10 | Registry revoke/suspend + FeatureGate + autonomy demote |

## 3. Component interfaces

```text
Product / worker client
  │  Bearer + x-aion-tenant-id
  ▼
aion-runtime Execution Gateway  (/v1/*)
  │  authenticate → Principal
  │  resolveDurableActor (registry revoke fail-closed)
  │  Agent Identity Registry (/v1/registry/*)
  ▼
aion-core PolicyEngine.authorize
  │  tenant · identity · registry · provenance · authority
  │  permission · data-scope · action-tier · risk · approval · budget
  ▼
aion-core Orchestrator / MissionOrchestrator
  │  ExecutionRegistry → adapters
  ▼
aion-data  (actors, executions, events, approvals, outcomes, …)
  │
  ▼
Secrets / vault (aion-infra) — credentials never stored on AgentActor
```

| Seam | Owner | Contract |
|---|---|---|
| Actor / Authority / ActionTier / Execution | `aion-core` | zod contracts |
| Policy decision | `aion-core` PolicyEngine | `PolicyDecision` |
| Persistence | `aion-data` | migrations + repositories |
| HTTP ingress + adapters | `aion-runtime` | gateway routes |
| Secret material | `aion-infra` | vault / env injection to Runtime only |
| Audit ledger | `aion-data` executions + events | SIS `execution_evidence` |

## 4. Threat model (v1)

| Threat | Attack | Mitigation |
|---|---|---|
| T1 Identity spoof | Body claims another agentId / agentUri | Durable Actor wins; identity check |
| T2 Tenant escape | Cross-tenant header or resource | Principal binding + PolicyEngine tenant-scope |
| T3 Privilege escalation | Body enlarges permissions/tools | Durable grants win; register role to mint |
| T4 Authority amplification | Child exceeds parent grant | `authoritySubsumes` / attenuate-only |
| T5 Observe→Execute | Observe agent mutates LoB | Action-tier deny |
| T6 Data exfil | Request outside data_scope | data-scope deny |
| T7 Approval replay | Reuse approval across runs | Approval binding checks |
| T8 Orphan agent | Ungoverned worker with keys | Registry review SIS-AG-10; no raw keys |
| T9 Credential theft from agent | Long-lived LoB key in worker | Vault / short-lived; adapter-side secrets |
| T10 Runaway spend / loops | Unbounded Execute | Budget + cost fields; rate/spend (GX-09) |
| T11 Failed contain | Cannot stop compromised agent | Registry revoke + FeatureGate kill + demote |
| T12 Provenance laundering | Untrusted instruction drives R2+ | Provenance quarantine / trust levels |

Residual risks (tracked): progressive credential isolation incomplete on some
adapters; Continuous Assurance loop (AIO-47); full `contain(agent_id)`
orchestration across IdP + vault + gateway still maturing.

## 5. Migration path (from current gateway)

| Phase | Work | Exit criteria |
|---|---|---|
| M0 | ADR-003 gateway in Runtime (done) | `/v1/commands` etc. live |
| M1 | Agent Identity Registry (AIO-44) | SIS-AG-02 schema + API + inventory |
| M2 | SEL contract (this doc / ADR-012) | Controls + threat model committed |
| M3 | Auth mode=`required` everywhere non-local | Principal on all product paths |
| M4 | Complete registry registration per tenant | Inventory export has zero incompletes for prod |
| M5 | Adapter credential isolation | No long-lived LoB keys in agent processes |
| M6 | Kill/contain runbook evidenced | Tabletop: revoke → deny → reactivate |
| M7 | Retire parallel control planes | O2/O3 paths gone or gateway-wrapped |

Products still embedding an in-memory control plane must become Runtime clients
(system-integration overview O2/O3).

## 6. Acceptance (AIO-45)

| Criterion | Evidence |
|---|---|
| ADR/spec committed | This file + ADR-012 |
| Threat model included | §4 |
| Clear interfaces Runtime/Core/adapters/secrets/audit | §3 |
| Migration path from current gateway | §5 |

## Related

- [agent-identity-registry.md](agent-identity-registry.md)
- [execution-layer.md](execution-layer.md)
- [security-model.md](security-model.md)
- [../governance/human-gates.md](../governance/human-gates.md)
