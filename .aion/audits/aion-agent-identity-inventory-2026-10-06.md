# AION Agent Identity Inventory — 2026-10-06

AIO-44 acceptance artifact (dated snapshot).

**Canonical living inventory:**
[../../governance/agent-identity-registry-inventory.md](../../governance/agent-identity-registry-inventory.md)

**Architecture / API:**
[../../architecture/agent-identity-registry.md](../../architecture/agent-identity-registry.md)

**Runtime export:** `GET /v1/registry/inventory` (completeness + SIS-AG-02 records).
**Governance review:** `GET /v1/registry/review?observed=…` (SIS-AG-10).

## Snapshot summary (2026-10-06)

| Class | Status |
|---|---|
| Runtime / platform agents (`revenue/*`, `media/*`, `infra/smoke`, …) | Inventoried; **register per env** via `PUT /v1/registry/agents` |
| Revenue Copilot / Workforce Control | Must use Runtime-registered actors (not in-memory control plane) |
| Cursor `.aion/agents/*` cards | Human engineering roles — out of Runtime registry scope |
| Parallel planes (legacy Revenue Copilot, Action Engine) | **Hard-stop** until gateway-wrapped or retired |

## Governance rule

Unknown or incomplete agents **fail** governance review. They may exist during
migration but must not be Execute-ready.
