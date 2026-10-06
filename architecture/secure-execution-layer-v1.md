# Secure Execution Layer v1

- **Status:** Spec (AIO-45)
- **Date:** 2026-10-06
- **Depends on:** [security-infrastructure-standard-v1.md](security-infrastructure-standard-v1.md) (AIO-43),
  Agent Identity Registry (AIO-44), [ADR-003](../adr/ADR-003-execution-gateway-into-runtime.md),
  [ADR-005](../adr/ADR-005-runtime-identity-plane.md), [ADR-007](../adr/ADR-007-observe-assist-execute-tiers.md)
- **Owners:** TechOps / Platform

## 1. Permanent architectural boundary

Sensitive execution always crosses the AION Action / Execution Gateway:

```text
Agent → AION Gateway → Policy → Business API
```

Never:

```text
Agent → raw API key → Business API
```

Raw CRM, accounting, DMS, email, or cloud credentials must progressively disappear
from agent runtimes (vaulted / short-lived / delegated credentials).

The gateway is an HTTP surface of `aion-runtime` (ADR-003) — not a second microservice.

## 2. Required controls

| # | Control | Enforcement surface |
|---|---|---|
| 1 | Authenticated principal before authorization | Runtime identity plane (ADR-005) |
| 2 | Tenant binding | Header is filter, not credential; durable Actor tenant wins |
| 3 | Tool allow-lists | `AgentActor.allowedTools` + PolicyEngine |
| 4 | Observe / Assist / Execute policy | `actionTier` + ADR-007 |
| 5 | Data-scope enforcement | `allowedData` + PolicyEngine data-scope checks |
| 6 | Human approval for sensitive actions | R2/R3 gates; financial/destructive always gated |
| 7 | Short-lived credentials where supported | Secrets architecture / adapters (AIO-45 implementation) |
| 8 | Secrets vault integration | `aion-infra` secret system; no secrets in repos |
| 9 | Full execution audit evidence | `executions` + Trust Score + registry `executionEvidence` |
| 10 | Rate / spend limits | Cost budget + economics rollups |
| 11 | Emergency revoke + kill / contain | Registry `revocationState` + FeatureGate + autonomy demote |

## 3. Interfaces

| Plane | Owns |
|---|---|
| **Core** | Actor / registry / authority / policy / Trust Score contracts |
| **Data** | Durable actors, executions, approvals, grants |
| **Runtime Gateway** | Authn, register/list/revoke agents, command/mission ingress |
| **Adapters** | Provider-specific calls using vaulted credentials — never ambient agent keys |
| **Audit ledger** | Execution objects + events + telemetry spine |

## 4. Threat model (v1)

| Threat | Mitigation |
|---|---|
| Stolen long-lived LoB API key in agent runtime | Credential isolation; gateway-held secrets; short-lived tokens |
| Agent self-escalation via request body | Durable Actor grants win (resolve-actor) |
| Cross-tenant access via header spoof | Principal tenant binding; RLS where enabled |
| Prompt injection → excessive write | Tier + tool/data allow-lists; human gates on R3 |
| Compromised agent continues acting | `revocationState` deny at resolve; kill-switch / demote |
| Unattributable automation | Registry completeness (SIS-AG-02); orphan agents fail review |

## 5. Migration path from current gateway

1. Inventory agents via `GET /v1/actors` registry export (AIO-44).
2. Complete SIS-AG-02 fields; `requireComplete: true` on register for production.
3. Move product in-process agents to Runtime clients.
4. Strip raw credentials from product/agent environments; adapters fetch from vault.
5. Enforce revoke/contain tabletop as part of customer-zero (AIO-46).

## 6. Acceptance (AIO-45)

| Criterion | Evidence |
|---|---|
| ADR/spec committed | This document |
| Threat model included | §4 |
| Clear interfaces Runtime / Core / adapters / secrets / audit | §3 |
| Migration path from current gateway | §5 |

## 7. Non-goals

- Second gateway microservice
- Full enterprise SIEM product
- OT/ICS cybersecurity implementation (discovery only in audits)
