# Agent Identity Registry

- **Status:** Active (AIO-44)
- **Standard:** [security-infrastructure-standard-v1.md](security-infrastructure-standard-v1.md) §3.9 (SIS-AG-01…10)
- **Contracts:** `@aion/core` `AgentActor` + `agent-registry.ts`
- **Durability:** `aion-data` `actors` (migration `0014`)
- **Management path:** `aion-runtime` `GET|PUT /v1/registry/*`

Every production agent / non-human worker must appear in this registry with
complete SIS-AG-02 fields before Execute-tier work is allowed under
auth mode=`required`.

## Mandatory fields (SIS-AG-02)

| SIS field | Core `AgentActor` field | Notes |
|---|---|---|
| `agent_id` | `agentUri` (preferred) or `agentId` | `agent://aion/{domain}/{role}/{id}` |
| `human_owner` | `owner` | Named accountable human |
| `business_purpose` | `purpose` | One-job purpose |
| `tenant` | `tenantId` | Header is filter, not credential |
| `permission_tier` | `actionTier` | Observe / Assist / Execute |
| `tools` | `allowedTools` | Exact allow-list |
| `data_scope` | `allowedData` | Data classes / systems |
| `delegated_authority` | `delegatedAuthority` | Traceable `DelegatedAuthority` |
| `policy_version` | `policyVersion` | Policy bundle pin |
| `execution_evidence` | `executionEvidence` | Link/path to runs / audit |
| `revocation_state` | `revocationState` | `active` \| `suspended` \| `revoked` |

Operational fields retained: `environment`, `credentialMethod`,
`approvalRequirements`, `lastActivity`.

## API (Runtime)

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/v1/registry/agents` | Tenant agent list + completeness |
| `GET` | `/v1/registry/agents/:actorId` | One entry |
| `PUT` | `/v1/registry/agents` | Register / update (`register` role) |
| `POST` | `/v1/registry/agents/:id/revoke` | Contain — `revoked` |
| `POST` | `/v1/registry/agents/:id/suspend` | Contain — `suspended` |
| `POST` | `/v1/registry/agents/:id/reactivate` | Restore — `active` |
| `GET` | `/v1/registry/review` | SIS-AG-10 governance review |
| `GET` | `/v1/registry/inventory` | SIS inventory export |

`GET /v1/actors` remains the lighter operator-launch list; the registry path is
the governance / audit surface.

## Enforcement

1. **Revocation** — `suspended` / `revoked` agents fail at
   `resolveDurableActor` and PolicyEngine `registry` check.
2. **Completeness** — Execute-tier capabilities require complete SIS-AG-02 when
   Runtime auth mode is `required` (or the agent has adopted `policyVersion`).
3. **Orphans** — `GET /v1/registry/review?observed=…` fails closed on unknown
   observed agent ids (SIS-AG-10).

## Inventory

Canonical static inventory of known AION agents:
[../governance/agent-identity-registry-inventory.md](../governance/agent-identity-registry-inventory.md).

Live inventory: `GET /v1/registry/inventory` against each environment.

## Related

- [ADR-007](../adr/ADR-007-observe-assist-execute-tiers.md) — permission tiers
- [ADR-008](../adr/ADR-008-authority-and-provenance-primitives.md) — delegated authority
- [ADR-012](../adr/ADR-012-secure-execution-layer-v1.md) — Secure Execution Layer (AIO-45)
- [agent-governance.md](../governance/agent-governance.md)
