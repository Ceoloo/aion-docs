# AION Agent Identity Inventory — 2026-10-06

AIO-44 acceptance artifact: all known AION agents inventoried against
[Security Infrastructure Standard v1](../../architecture/security-infrastructure-standard-v1.md)
SIS-AG-02 fields.

**Source of truth at runtime:** `GET /v1/actors` (registry export) once agents
are registered in Data. This document is the curated baseline inventory for
customer-zero and governance review.

## Mandatory fields checklist

`agent_id` · `human_owner` · `business_purpose` · `tenant` · `permission_tier` ·
`tools` · `data_scope` · `delegated_authority` · `policy_version` ·
`execution_evidence` · `revocation_state`

## Inventory

| Agent / worker | Kind | Owner | Tenant | Tier | Registry complete? | Notes |
|---|---|---|---|---|---|---|
| Revenue Copilot product agent | Product in-process | Product eng | (varies) | Assist-intended | **No** | Built via `createAgentActor` in products; must migrate to Runtime registration |
| Workforce Control registered agents | Runtime actors | Operator-registered | Console tenant | Declared at register | **Partial** | Completeness depends on register payload; use `requireComplete: true` |
| Cursor / `.aion/agents/*` role cards | Human specialist prompts | Engineering | n/a | n/a | n/a | Not runtime workers — out of registry scope |
| Test fixtures (`makeAgent`, gateway tests) | Test-only | platform-team | aion-test | Assist | Yes (fixtures) | Not production |

## Orphans / unknown

| Finding | Action |
|---|---|
| Product-local agents not in Runtime `actors` | Register via `POST /v1/actors` before Execute-tier work |
| Agents missing tenant / owner | Fail governance review (`orphanCount` on inventory export) |
| Agents without `policy_version` / `execution_evidence` / `revocation_state` | Complete fields; block with `requireComplete` in production |

## Governance rule

Unknown or incomplete agents **fail** governance review. They may exist during
migration but must not be Execute-ready (audit hard stops apply).

## Follow-on

- AIO-45 Secure Execution Layer — enforce gateway credential isolation
- AIO-46 customer-zero scored audit — use this inventory as AI/Agents evidence
