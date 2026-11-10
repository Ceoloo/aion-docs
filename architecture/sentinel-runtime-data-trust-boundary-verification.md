# Sentinel → Runtime → Data Trust-Boundary Verification Plan

- **Status:** Active plan (not an approval)
- **Date:** 2026-11-09
- **Brief:** [../.aion/research/techops-monday-brief-2026-11-09.md](../.aion/research/techops-monday-brief-2026-11-09.md)
- **Linear:** [AIO-121](https://linear.app/aion-empire/issue/AIO-121/p0-forge-persistent-keycloak-staging-authorization-security-sign-offs) · [AIO-122](https://linear.app/aion-empire/issue/AIO-122/aio-121-security-approval-independently-review)
- **Runtime draft:** `aion-runtime/docs/sentinel-keycloak-gateway.md`
- **Data draft:** `aion-data/docs/sentinel-identity-bindings.md`
- **Evidence contract:** [agent-security-evidence-contract.md](agent-security-evidence-contract.md)

This plan is the **verification gate** for the Sentinel → Runtime → Data
identity boundary. Completing the checklist produces evidence for human
approvers. It does **not** authorize merge, migration, staging deploy, Forge
workers, or production.

```text
CI green ≠ interoperable ≠ staging-approved ≠ production-ready
```

## 1. Trust-boundary statement

```text
Bearer JWT
  → pinned Sentinel Keycloak RS256 verification
  → aion_auth.principal_bindings (exact iss / sub / azp)
  → durable actors
  → Runtime Principal
  → existing tenant + policy + human-approval gates
```

| Component | Owns | Must not own |
|---|---|---|
| **Sentinel** | Token signature / issuer / audience / client allow-list verification | Tenant grants, actor minting, policy, approvals |
| **Runtime** | Gateway authn → Principal; fail-closed on missing binding / revoked actor | Self-provisioning from roles/email/groups/headers |
| **Data** | Durable `actors` + `aion_auth.principal_bindings`; SELECT-only for `aion_app` | Public GraphQL/PostgREST/agent write to bindings |
| **Keycloak** | Staging realm IdP (HTTPS, MFA, short-lived tokens) | AION policy decisions |

No self-provisioning. No dynamic grants from token claims. Binding defaults
**disabled** until an auditable admin activation outside Runtime.

## 2. Preconditions (blockers)

Do not start persistent staging probes until all are true:

| # | Precondition | Evidence |
|---|---|---|
| P1 | Named operator selects non-production GCP staging project, region, network boundary, cost budget, tenant fixture, window, rollback owner | Signed slot on AIO-121 |
| P2 | Named **independent** security reviewer assigned (AIO-122); no AI auto-approval | Reviewer identity recorded |
| P3 | Named infrastructure maintainer assigned for digest / migration / Keycloak secret handling | Maintainer identity recorded |
| P4 | Infra staging auto-deploy risk understood; protected Environment / manual dispatch preferred ([aion-infra#26](https://github.com/Ceoloo/aion-infra/pull/26) or equivalent) | Policy decision recorded |
| P5 | Disposable CI Keycloak evidence exists and is labeled **CI-only** (not staging approval) | Artifact links on AIO-121 |
| P6 | Data #30 (raw pool tenant-scope bypass) reviewed or explicitly residual-risk accepted for staging only | Issue link + decision |

## 3. Dependency merge order

Human-approved merges only; each step re-pins consumers before the next.

| Step | Repository / PR | Exit evidence |
|---|---|---|
| D1 | Sentinel PR (pinned package + checksum + negative unit tests) | Reviewed SHA / digest |
| D2 | Data migrations **0015** (bindings) + **0016** (append-only security evidence) on disposable DB | Privilege tests: `aion_app` SELECT-only on `aion_auth`; no public/client write |
| D3 | Runtime Keycloak gateway opt-in + Forge audit-only bridge (workers **OFF**, always 409 after audited request) | Review + image digest |
| D4 | Runtime real-Keycloak interoperability suite (HTTPS JWKS, rotation, expiry, logout limits, IdP outage, RLS readback) | CI artifact retained; still not staging approval |
| D5 | Infra staging release guard (no accidental push-to-deploy for this gate) | Maintainer sign-off |
| D6 | Refresh Runtime dependency pins to independently reviewed main commits; rerun acceptance/image pipeline | Pin diff + green verify |

## 4. Staging probe sequence (synthetic only)

Authorized operators only. Synthetic identities. Short-lived secrets. No
privileged agent shell. No customer data. Forge workers remain **DISABLED**.

### 4.1 Environment bring-up

1. Apply migrations via authorized migrator (not Runtime DB URL).
2. Confirm grants: `aion_app` has `USAGE` on `aion_auth` + `SELECT` on
   `principal_bindings` only; no INSERT/UPDATE/DELETE.
3. Stand up Keycloak staging realm: HTTPS, audience mapper, Auth Code + PKCE,
   MFA, access-token max age ≤ 5 minutes, no real customer identities.
4. Provision **disabled** test bindings through operator-controlled admin route
   outside Runtime; enable only the fixtures needed for the probe window.
5. Set Runtime staging:

```text
AION_AUTH_PROVIDER=keycloak
AION_AUTH_MODE=required
AION_KEYCLOAK_ISSUER=<exact HTTPS realm>
AION_KEYCLOAK_AUDIENCE=aion-runtime
AION_KEYCLOAK_ALLOWED_CLIENTS=<exact clients>
AION_GATEWAY_API_KEYS=<empty>
```

### 4.2 Positive path (must pass)

| Probe | Expected |
|---|---|
| Valid JWT → Sentinel verify → active binding → durable actor → Principal | Authenticated request reaches policy boundary |
| Tenant-scoped command within grants | Allowed under existing policy |
| Append-only security / execution evidence write + readback | Durable row; tenant isolation held |
| Forge audit-only bridge (if in scope) | Audited request recorded; **409**; no worker execution |

Each consequential probe must emit an
[Agent Security Evidence Contract](agent-security-evidence-contract.md) record
(or equivalent pack fields) covering:

```text
agent_id → principal → tenant → policy → requested action
  → authorization → execution trace → result → verification
```

### 4.3 Negative / fail-closed probes (must deny)

| Probe | Expected |
|---|---|
| Expired token | Deny |
| Wrong issuer / audience / client (`azp`) | Deny |
| JWKS key rotation (old key overlap + new key remote reload) | Accept only currently valid keys; no panic |
| Disabled / revoked / expired binding | Deny |
| Revoked / suspended durable actor | Deny |
| Cross-tenant header or resource | Deny |
| Missing Data / IdP outage (cold verifier + warm verifier) | Deny |
| Backchannel logout alone (signed token still unexpired) | Document residual; **binding disable / introspection** required for immediate contain |
| Non-empty API keys while Keycloak required | Config reject / fail closed |
| Attempted self-provision from token roles/email/groups | Deny; no actor mint |

### 4.4 Rollback drill (must evidence)

1. Flip `AION_AUTH_PROVIDER=api-key` (or prior trusted mode).
2. Restore last trusted Runtime image digest.
3. Disable / revoke test bindings.
4. Confirm migrations remain forward-only (no drop without approved follow-up).
5. Record time-to-rollback and who executed it.

## 5. Evidence pack (required before any R3 decision)

Store under operator-controlled evidence location; link from AIO-121.

| Artifact | Contents |
|---|---|
| Revision matrix | Exact SHAs / digests for Sentinel, Data, Runtime, Infra, Keycloak image |
| Config redaction | Issuer URL pattern, audience, clients — **no secrets** |
| Probe log | Pass/fail table for §4.2–§4.4 with timestamps |
| Evidence contract samples | ≥1 positive + ≥1 denied consequential execution pack |
| Privilege proof | DB grant query output for `aion_auth` |
| Rollback proof | Provider flip + digest restore timestamps |
| Residual risks | Logout semantics, Data #30, pin draft SHAs, anything deferred |

## 6. Approval slots (human only)

| Slot | Role | Authorizes |
|---|---|---|
| S1 | Operator | Staging target + budget + window |
| S2 | Independent security reviewer | Threat model + chain of trust (AIO-122) |
| S3 | Infrastructure maintainer | Digests, migrations, Keycloak secrets, readiness |
| S4 | Operator execution auth | Run the specified staging probe payload only |
| S5 | Final R3 operator decision | Approve/deny **this staging test** — never Forge workers or production |

Missing any slot ⇒ boundary remains **NOT APPROVED**.

## 7. Explicit non-authorizations

Until S1–S5 are complete and recorded:

- No production Keycloak cutover
- No Forge v0.3 worker enablement (AIO-111 / GE-001 remain independent gates)
- No inference that CI Keycloak green == staging interoperability
- No bypass of staging Environment protection for convenience

## 8. Mapping to Nov 9 packaging

| Brief action | This plan |
|---|---|
| Sentinel Identity — verified agent identity, delegated authority, revocation | §1 + §4.3 binding/actor revoke probes |
| Runtime + Data — durable execution evidence + tenant isolation | §4.2 evidence write/readback + negative cross-tenant |
| Secure Execution Layer — authorization-bound execution | Evidence contract mandatory on probes |
| Engineering dependency warning | Entire document — gates before production claims |

## Related

- [ADR-005](../adr/ADR-005-runtime-identity-plane.md)
- [secure-execution-layer-v1.md](secure-execution-layer-v1.md)
- [agent-identity-registry.md](agent-identity-registry.md)
- [../governance/human-gates.md](../governance/human-gates.md)
