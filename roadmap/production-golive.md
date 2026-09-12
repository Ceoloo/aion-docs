# Production go-live — OL-001 ready state

Status snapshot **2026-09-12**. This is the ordered close-out for live Revenue
OS + OL-001 M001. Ops rollout first; mission credit only after Runtime tip is
serving and Console close path works.

## Verified live now

| Surface | Status |
|---|---|
| Operator Console | ✅ https://aion-operator-console.vercel.app — SPA routes (`/ol001`, `/missions/…`) READY; close UI shipped (`aion-products` #23) |
| Production Runtime health | ✅ `https://runtime.srv1655818.hstgr.cloud/health/ready` — DB up, `service_version=0.2.1` |
| Production Runtime tip | ❌ still `git_sha=3329c01…` — **must advance** to tip with #34/#35/#36 |
| Traefik edge CORS methods | ❌ `GET,POST,OPTIONS` only — **blocks PATCH** mission close even after Runtime tip |
| Tip Runtime image (GHCR) | ✅ **`execution-platform-v0.2.2`** @ `sha256:b12043ad9be4937dc00345ffe973d61120921492c76b4d5806fa6a7e99eda5ee` (also tagged `75e9acd…`) |
| `aion-data` revenue_sessions | ✅ migration `0009` on `main` — still needs prod migrate on next Runtime roll |
| Preferred DNS `runtime.aionsystems.ai` | ❌ unresolved — keep clients on Hostinger FQDN |

## Blockers (ordered)

### 1. Sync Traefik CORS PATCH on the VPS (code landed; host must update)

Edge labels in `aion-infra` `providers/vps/docker-compose.yml` must allow
`GET,POST,PATCH,OPTIONS`. `deploy-vps.yml` does **not** sync compose from git —
copy the updated file to `/opt/aion/docker-compose.yml` before the image roll.

Verify after roll:

```bash
curl -si -X OPTIONS https://runtime.srv1655818.hstgr.cloud/v1/missions \
  -H 'Origin: https://aion-operator-console.vercel.app' \
  -H 'Access-Control-Request-Method: PATCH' \
  -H 'Access-Control-Request-Headers: content-type,authorization' \
  | tr -d '\r' | grep -iE 'HTTP/|access-control-allow'
# Expect methods include PATCH and Allow-Origin = Console origin
```

### 2. Deploy Runtime tip (human gate)

Do **not** approve a blank `deploy-vps` that resolves only
`execution-platform-v0.2.1` (`1f5d1ce…`) — that is behind tip.

Preferred pin (after `execution-platform-v0.2.2` publishes, blank pin is fine):

| Input | Value |
|---|---|
| `environment` | `production` |
| `runtime_image` | `ghcr.io/ceoloo/aion-runtime@sha256:0e103dd5e79c5cac84fcc440cce2154944183d879ffd29283886be35bc1885c1` |
| `git_sha` | `75e9acd841f036c7f6a78cbcd7f0bdbe2c6d0ae1` |

Or pin `execution-platform-v0.2.2` once CI publishes that release tag.

Confirm `/` and `/health/ready` show `git_sha` starting with `75e9acd` (not
`3329c01`).

Pending production approval:
https://github.com/Ceoloo/aion-infra/actions/runs/34665039054 — reject if it
would roll `v0.2.1`; re-dispatch with the tip pin above.

### 3. Close OL-001 M001 (do not stall)

Per [operating-leverage.md](./operating-leverage.md) M001 close sequence:

1. One `crm.task.create` retry after tip Runtime is live
2. Pass → complete normally; fail → visible waiver `completed_with_exception`
3. Launch M002 — do not over-engineer task.create

Console close buttons need tip Runtime **and** Traefik PATCH CORS.

### 4. Optional / deferred (not M001 blockers)

| Item | Notes |
|---|---|
| OpenRouter / Revenue Copilot profile | Model-backed proofs; install key on host, do not paste into chat |
| Vercel `ceoloo-aion-revenue-copilot` | Still linked to `AION-Sys/Ceoloo-aion-revenue-copilot`, not `aion-products` |
| `v0-ai-growth-operator` | No GitHub source — map or retire |
| DNS cutover `runtime.aionsystems.ai` | After Traefik host ready |

## Operator checklist (copy/paste)

```text
[ ] Merge aion-infra Traefik CORS PATCH PR
[ ] scp updated docker-compose.yml → /opt/aion/docker-compose.yml
[ ] Reject wrong waiting deploy-vps if pinned to v0.2.1 only
[ ] Approve deploy-vps with tip digest (or v0.2.2) + git_sha 75e9acd…
[ ] Confirm https://runtime…/health/ready git_sha advances
[ ] Confirm OPTIONS … PATCH returns Allow-Methods including PATCH
[ ] Console: one crm.task.create retry on M001
[ ] Console: complete or completed_with_exception + launch M002
```

## Invariant

Production readiness for this slice = Console + tip Runtime + edge PATCH CORS +
honest M001 terminal record. Partial compliance stays labelled prototype / paused.
