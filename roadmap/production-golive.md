# Production go-live — Track A authenticated release

Status snapshot **2026-09-14**. AION is a **controlled internal production
pilot**, not broad client operations. This checklist is the ordered close-out
for one **authenticated, compatible** production release before any further
client-facing surface change.

Supersedes the 2026-09-12 draft that pinned only `execution-platform-v0.2.2`
(`75e9acd…`). That tip predates identity, durable outcomes/sessions proof, and
single-process request coalescing on Runtime `main`.

## Evidence posture

| Layer | Position |
|---|---|
| Documented architecture | Strong |
| Implemented code | Substantial |
| Tested platform mechanics | Strong |
| Verified live operation | Narrow / partially operator-reported |
| Proven revenue production | Not established |

## Track A goal

Ship **one** immutable production digest where these land together:

1. Runtime tip on/after identity + durability + concurrency (`main` includes
   `#37` outcomes/sessions, `#38` restart-stable executions, `#41` identity
   plane, `#40` same-`requestId` coalescing).
2. Infra VPS compose injects `AION_AUTH_MODE` + `AION_GATEWAY_API_KEYS`, and
   Traefik CORS allows `PATCH`.
3. Products Copilot / server Runtime client sends `Authorization: Bearer …`
   and authenticated tenant context; staging/production fail closed without
   `AION_RUNTIME_URL` (ADR-007).
4. Migrations applied; deny/allow auth verified; release SHA recorded; rollback
   and alerts checked before changing the client-facing surface.

**Do not** deploy Runtime-required-auth alone. Missing keys → startup failure;
missing Products bearer → products cannot call Runtime.

## Current Runtime tip (code + published image)

| Ref | Value |
|---|---|
| Runtime `origin/main` (as of checklist authoring) | `eb36cfb63b33587fc79840ba064d63be92106892` (`#40` on top of `#41`) |
| Tip image (CI publish on that SHA) | `ghcr.io/ceoloo/aion-runtime@sha256:39b423175d03423e90918772541e8b9830d8a1ee980050ae380165d04ef11c2c` (also tagged `eb36cfb63b33587fc79840ba064d63be92106892`) |
| Last **release tag** observed | `execution-platform-v0.2.2` @ `75e9acd…` / digest `sha256:b12043ad9be4937dc00345ffe973d61120921492c76b4d5806fa6a7e99eda5ee` — **insufficient for Track A** |

Prefer digest-pinning the tip image above (or a later tip). Optionally cut
`execution-platform-v0.2.3` (or next) after host verify — do not treat `v0.2.2`
as the Track A pin.

## Blockers (ordered)

### 1. Land coordinated PRs (merge order)

| Order | Repo | Track A change |
|---|---|---|
| 1 | `aion-infra` | Traefik `PATCH` CORS + pass `AION_AUTH_MODE` / `AION_GATEWAY_API_KEYS` into Runtime; Copilot gets `AION_RUNTIME_API_KEY` |
| 2 | `aion-products` | Fail-closed Runtime URL + bearer/tenant on server Runtime client |
| 3 | `aion-runtime` | Publish digest from tip ≥ `eb36cfb…` (or successor); tag when certified |
| 4 | `aion-docs` | This checklist + recorded digest/SHA after verify |

### 2. Sync host compose (human)

`deploy-vps.yml` does **not** sync `docker-compose.yml` from git. After Infra
lands:

```bash
scp providers/vps/docker-compose.yml root@<vps>:/opt/aion/docker-compose.yml
```

Edit `/opt/aion/.env` (root, `0600`) — never commit real values:

- `AION_AUTH_MODE=required`
- `AION_GATEWAY_API_KEYS` — JSON array per ADR-005 / Runtime `auth/config.ts`
  (service principal for Copilot with `invoke`; operator principal with
  `approve`+`invoke` as needed)
- `AION_RUNTIME_API_KEY` — must match a token in that JSON (Copilot profile)
- `AION_TENANT_ID` — ⊆ principal `tenantIds` (pilot: `aion-systems`)

### 3. Deploy tip digest (human gate)

| Input | Value |
|---|---|
| `environment` | `production` |
| `runtime_image` | **new** tip digest / release tag (not `v0.2.2` alone) |
| `git_sha` | tip SHA (≥ `eb36cfb…`) when digest-only pin requires it |

Reject any waiting deploy that would roll only `v0.2.1` / `v0.2.2` without
identity.

### 4. Verify before client-facing change

```bash
# Ready + SHA
curl -sS https://runtime.srv1655818.hstgr.cloud/health/ready | jq .

# CORS PATCH preflight (Console mission close)
curl -si -X OPTIONS https://runtime.srv1655818.hstgr.cloud/v1/missions \
  -H 'Origin: https://aion-operator-console.vercel.app' \
  -H 'Access-Control-Request-Method: PATCH' \
  -H 'Access-Control-Request-Headers: content-type,authorization' \
  | tr -d '\r' | grep -iE 'HTTP/|access-control-allow'

# Auth deny (no bearer) — expect 401 when mode=required
curl -si https://runtime.srv1655818.hstgr.cloud/v1/services \
  -H 'x-aion-tenant-id: aion-systems' | head -n1

# Auth allow (bearer + tenant ⊆ principal) — expect 2xx with valid key
curl -si https://runtime.srv1655818.hstgr.cloud/v1/services \
  -H "Authorization: Bearer $AION_RUNTIME_API_KEY" \
  -H 'x-aion-tenant-id: aion-systems' | head -n1
```

Also confirm: migrate-before-roll completed; prior digest rollback path still
understood (migrations already applied — schema compatibility risk remains);
alerts deliver.

## Explicitly out of Track A

| Item | Notes |
|---|---|
| Active Copilot mid-call restart recovery | Track C — storage exists; live `Map` still authoritative |
| ModernRelx M001 measured close | Track B — after Track A Runtime is reachable with model |
| AIO-17 live merge | Runtime #42 draft — after Track A |
| Operator Console browser bearer | Do **not** bake gateway secrets into `VITE_*`; BFF/session token follow-on |
| FORCE RLS / multi-process idempotency / TLS verify | Reliability backlog |
| Desks live Stripe purchase | Cash experiment — defer engineering until A/B land |

## Operator checklist (copy/paste)

```text
[ ] Merge aion-infra Track A (CORS PATCH + gateway key injection)
[ ] scp updated docker-compose.yml → /opt/aion/docker-compose.yml
[ ] Set AION_AUTH_MODE + AION_GATEWAY_API_KEYS (+ Copilot AION_RUNTIME_API_KEY) in /opt/aion/.env
[ ] Merge aion-products Track A (fail-closed URL + bearer/tenant client)
[ ] Publish Runtime tip image; record digest + git_sha below
[ ] Approve deploy-vps with tip digest (reject v0.2.2-only if it lacks identity)
[ ] Confirm /health/ready git_sha advances to tip
[ ] Confirm OPTIONS … PATCH allows PATCH
[ ] Confirm unauthenticated /v1/* denied; bearer+tenant allowed
[ ] Confirm Copilot/products can reach Runtime with the injected key
[ ] Record digest/SHA in this file; only then change client-facing surface
[ ] Proceed to Track B (M001 close) — do not count launched missions as success
```

## Release pin (fill after verify)

| Field | Value |
|---|---|
| Release tag | _TBD — cut after tip publish_ |
| Image digest | `ghcr.io/ceoloo/aion-runtime@sha256:`_TBD_ |
| Runtime git_sha | _TBD (≥ eb36cfb…)_ |
| Infra compose SHA | _TBD_ |
| Products SHA | _TBD_ |
| Verified at (UTC) | _TBD_ |
| Verified by | _operator_ |

## Invariant

Production readiness for this slice = **aligned auth** + tip Runtime + edge
PATCH CORS + Products bearer + honest verification evidence. Partial compliance
stays labelled prototype / paused. Launched missions are not successful missions.
