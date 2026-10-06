# AION Agent Identity Registry — Production Inventory

- **Issue:** AIO-44
- **Date:** 2026-10-06
- **Standard:** SIS-AG-02 / SIS-AG-10
- **Live export:** `GET /v1/registry/inventory` (per environment)

This document inventories **known AION agents and non-human workers**. Rows
marked **Incomplete** fail SIS-AG-02 completeness until registered via
`PUT /v1/registry/agents`. Rows marked **Parallel / ungoverned** are hard-stop
findings for governance review (SIS-AG-10) until migrated onto the Execution
Gateway.

## 1. Runtime / platform agents (must be registry-complete for Execute)

| agent_id (canonical) | human_owner | purpose | tenant | tier | status |
|---|---|---|---|---|---|
| `agent://aion/revenue/copilot/*` | Revenue / platform owner | CRM assist + governed mutations | per-tenant | Assist→Execute | **Register** — used in Mission proofs, GHL, Revenue Copilot |
| `agent://aion/revenue/observer/*` | Revenue / platform owner | Limited observe path (Mission 004) | per-tenant | Observe | **Register** |
| `agent://aion/media/producer/*` | Media / platform owner | Mission 002 media production | per-tenant | Assist/Execute | **Register** |
| `agent://aion/media/observer/*` | Media / platform owner | Mission 002 observe | per-tenant | Observe | **Register** |
| `agent://aion/infra/smoke/*` | Platform / Runtime owner | Boot self-check | `aion-internal` | Observe | **Register** |
| `agent://aion/systems/attacker/*` | Security / Runtime owner | Mission 003 attack suite only | test tenants | Observe | **Register** (non-prod) |
| `agent://aion/revenue/grok-worker/*` | Revenue / platform owner | Grok runtime client worker | per-tenant | Assist | **Register** |

Fill `delegated_authority`, `policy_version` (`sis-v1.0/…`),
`execution_evidence` (`executions?actor_id={actor_id}`), `tools`, and
`data_scope` at registration. Default `revocation_state=active`.

## 2. Product control-plane agents

| Name | Path / system | Registry posture |
|---|---|---|
| **RevenueCopilot** | `aion-products` shared-execution / Runtime client | Must use Runtime registry actor; in-memory control plane is **not** a registry |
| Workforce Control launch agents | Operator UI → `GET /v1/actors` | Launch only agents returned by registry/actors for the tenant |

## 3. Cursor engineering team (docs cards — not Runtime actors)

These are **human-operated Cursor agents** under
[`../.aion/agents/`](../.aion/agents/). They are **not** production Runtime
workers and do **not** receive Execute-tier LoB credentials. Tracked here so
governance review does not confuse them with Runtime actors.

| Card | Role |
|---|---|
| `00-orchestrator` … `12-research-engineer` | Engineering OS roles (13 cards) |

If any Cursor agent is later given production Execute authority, it **must** be
promoted into §1 with full SIS-AG-02 fields.

## 4. Parallel / ungoverned (hard-stop until remediated)

| System | Finding | Remediation |
|---|---|---|
| Legacy `AION-Sys/Ceoloo-aion-revenue-copilot` | Bypasses Execution Gateway (system-integration O2) | Migrate to Runtime client; revoke parallel keys |
| Action Engine SQLite / non-gateway paths | Parallel execution (O3) | Retire or wrap behind gateway |
| Any agent with raw LoB API keys in process env | SIS-GX-07 / SIS-CT-07 gap | Vault / short-lived / delegated credentials (AIO-45) |

Observed agent ids that do not appear in §1 fail
`GET /v1/registry/review?observed=…`.

## 5. Evidence links

| Control | Evidence |
|---|---|
| SIS-AG-02 schema | `aion-core` `agent-registry.ts`; `aion-data` migration `0014` |
| Management path | `aion-runtime` `/v1/registry/*` |
| Policy fail-closed | PolicyEngine `registry` check; `resolveDurableActor` revoke deny |
| This inventory | This file + live `GET /v1/registry/inventory` |

## 6. Registration checklist

For each §1 agent in each environment (staging → production):

1. Mint / load `DelegatedAuthority` root from the human owner.
2. `PUT /v1/registry/agents` with complete SIS fields.
3. Confirm `GET /v1/registry/inventory` lists the row under `records`.
4. Confirm `GET /v1/registry/review` has no hard-stops for that tenant.
5. Exercise revoke → denied command → reactivate (SIS-AG-09 tabletop).
