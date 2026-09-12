# ADR-005: Runtime Identity Plane (Authn → Authz)

- **Status:** Accepted
- **Date:** 2026-09-12
- **Decision Owners:** Security / Permissions Engineer

## Context

Mission 003 established tenant/identity/approval isolation inside
`PolicyEngine.authorize`, but the Runtime gateway still accepted a
caller-supplied `Actor` (including `permissions`, `tenantId`, and risk ceiling)
and persisted it as authority. Likewise, `x-aion-tenant-id` was treated as a
credential rather than a filter, and approval decisions trusted body-supplied
`decidedBy` / `actor` without a verified human principal.

This violates least privilege and the security model invariant: **no ambient
authority; Runtime establishes permission; agents never self-assert grants.**

## Decision

1. **Authentication is a Runtime boundary concern.** The Execution Gateway
   authenticates a **Principal** from a bearer credential
   (`Authorization: Bearer …`) before any governed route runs.
2. **Authorization continues to evaluate durable Actors in Data/Core.** After
   authn, Runtime loads the Actor by id from Data. Body-supplied permissions /
   tenant / risk ceiling are evidence only — **Data wins**.
3. **`AION_AUTH_MODE`**
   - `open` (local default): proofs may omit bearer; first registration from
     body is allowed; subsequent calls cannot escalate durable grants.
   - `required` (staging/production default): bearer mandatory; principal
     `actorId` must match the command actor; `register` / `approve` / `invoke`
     are explicit principal roles.
4. **Tenant header is non-authoritative.** When a Principal is present,
   `x-aion-tenant-id` must be ⊆ `principal.tenantIds`. Cross-tenant resource
   mismatch remains DENY.
5. **Approval decisions require a human Actor.** In `required` mode the
   principal must hold `approve`, `decidedBy` must equal `principal.actorId`,
   and the durable Actor must have `actorType: human`.
6. **Data-class least privilege (gap 4)** is enforced in Core:
   `AuthorizationRequest.resourceDataClasses` must be ⊆ `AgentActor.allowedData`
   when present (fail closed on missing grants).
7. **Postgres RLS (gap 3)** remains a deliberate follow-on: Data stays the
   integrity store; an ADR-scoped migration will enable tenant RLS keyed on
   `current_setting('aion.tenant_id')` without replacing Core policy.

## Alternatives Considered

- **Full OIDC/mTLS in V1** — Rejected for now; bearer API keys mint the same
  Principal shape so OIDC can replace credential verification later without
  changing authorize().
- **Trust body Actor when signed** — Rejected; signing still lets callers mint
  grants. Durable registry is the source of authority.
- **Enable RLS immediately without session GUC wiring** — Rejected; would break
  the app role until Runtime sets `aion.tenant_id` on every connection.

## Consequences

### Positive

- Forged high-privilege actor bodies fail closed when auth mode is required.
- Tenant header forgery cannot expand principal scope.
- Approval self-approval / spoofed human identity is blocked at the gateway.
- `allowedData` becomes an enforceable control, not documentation only.

### Negative

- Local proofs and clients must eventually present API keys in non-open modes.
- Actor registration becomes an explicit privileged operation (`register` role).
- RLS still absent at the SQL boundary until the follow-on migration lands.

## Implementation Notes

- Runtime: `src/auth/*`, gateway principal binding, `RuntimeConfig.auth`.
- Core: `resourceDataClasses` + `data-scope` policy check.
- Env: `AION_AUTH_MODE`, `AION_GATEWAY_API_KEYS` (JSON array of token→principal).
- Follow-on: Data migration `0010_tenant_rls.sql` + Runtime `SET LOCAL
  aion.tenant_id` on request-scoped connections.

## References

- `architecture/security-model.md`
- `governance/permissions.md`
- `governance/human-gates.md`
- ADR-003 (execution gateway into runtime)
