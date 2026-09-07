# OPS-001 — Live AION Runtime endpoint

**Stand up the first live AION Runtime on the VPS, behind Traefik, with health,
DB connectivity, immutable image deployment, and GitHub-controlled production
release.**

Do **not** point the Operator Console at a stub Runtime. Provision Runtime first,
verify HTTPS, then wire Vercel.

Immutable platform baseline remains [`execution-platform-v0.2.0`](../releases/execution-platform-v0.2.0.manifest.json).
This is an **operations** milestone, not an architecture mission.

---

## Host inventory (Hostinger)

| Field | Value |
|---|---|
| Provider | Hostinger VPS (Boston) |
| Public IP | `69.62.70.182` |
| SSH user | `root` (prefer a non-root deploy user after bootstrap) |
| Edge (observed) | Traefik — `:80` → 308 to HTTPS; `:443` answers (unmatched Host → 404) |
| Preferred hostname | `runtime.aionsystems.ai` → `A`/`AAAA` to `69.62.70.182` |
| Operator SSH pubkey | `ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDBShw6Q06tsGaNKxexcOzqwRH1thuID0W/6mJkjXEIg 15168@CEO-Loo` |
| Pubkey fingerprint | `SHA256:C9hzbv3jzKYS+SaGaM40EHsA1+coDl9fM4AmxEFnrfs` |

Do **not** commit **private** SSH keys or DB passwords. Public keys are inventory only.
Put secrets in `/opt/aion/.env` (`0600`) and GitHub Environment secrets
(`VPS_HOST`, `VPS_USER`, `VPS_SSH_KEY` = matching **private** key PEM/OpenSSH form).

### SSH unlock (required before agent/CI can deploy)

1. On the VPS (Hostinger console or an existing root session), ensure that pubkey is in
   `/root/.ssh/authorized_keys` (or the deploy user’s).
2. In GitHub → `aion-infra` → Settings → Environments → `production` (or repo secrets):
   - `VPS_HOST=69.62.70.182`
   - `VPS_USER=root` (or deploy user)
   - `VPS_SSH_KEY=<private key that matches the pubkey above>` — never paste the private key into chat or commit it
3. DNS: `runtime.aionsystems.ai` → `69.62.70.182`
4. Then CI `deploy-vps.yml` or a session with that private key can run `deploy.sh`

---

## Topology

```text
DNS
  ↓
runtime.aionsystems.ai          ← stable hostname (not Hostinger FQDN)
  ↓
Traefik :443                    ← existing host edge (one edge only — no Caddy)
  ↓
aion-runtime :8080              ← Docker; not published on the host
  ↓
AION Data / Postgres            ← Mode A local or Mode B managed
```

Then:

```text
Vercel Operator Console
  ↓
VITE_AION_RUNTIME_URL=https://runtime.aionsystems.ai
```

Repo profile: [`aion-infra/providers/vps/`](https://github.com/Ceoloo/aion-infra/tree/main/providers/vps)
(Traefik labels; Caddy legacy profile only).

---

## Implementation order

1. **Provision `/opt/aion/.env`** (root-owned `0600`) from `.env.example`:
   - `AION_ENVIRONMENT=production`
   - `DATABASE_URL` (app role) + `MIGRATION_DATABASE_URL` (migrate one-shot only)
   - immutable `AION_IMAGE=ghcr.io/ceoloo/aion-runtime:<sha>`
   - `GIT_SHA`, `SERVICE_VERSION`
   - `AION_DOMAIN=runtime.aionsystems.ai`
   - `AION_TRAEFIK_NETWORK` / `AION_TRAEFIK_ENTRYPOINT` / `AION_TRAEFIK_CERT_RESOLVER`
     matching the host Traefik
   - `AION_CORS_ORIGINS=` (set after Vercel project exists)

2. **Runtime container on internal Docker network only** — do not publish `:8080`.

3. **Traefik router** via compose labels (or file-provider snippet under
   `providers/vps/traefik/`). Confirm Docker network exists:
   `docker network ls | grep -E 'traefik|proxy|web'`.

4. **DNS** — `A`/`AAAA` for `runtime.aionsystems.ai` → VPS IP.

5. **Deploy** — `cd /opt/aion && ./scripts/deploy.sh`  
   or GitHub Actions `deploy-vps.yml` (preferred for production).

6. **Verify**
   - `GET https://runtime.aionsystems.ai/health/live`
   - `GET https://runtime.aionsystems.ai/health/ready`
   - `GET https://runtime.aionsystems.ai/` (release metadata / git_sha)
   - one `GET /v1/...` with `x-aion-tenant-id` (missing header → DENY)

7. **GitHub production Environment** — secrets `VPS_HOST`, `VPS_USER`, `VPS_SSH_KEY`;
   required reviewers for production.

8. **Only then** — create Vercel `aion-operator-console`:
   - `VITE_AION_RUNTIME_URL=https://runtime.aionsystems.ai`
   - set Runtime `AION_CORS_ORIGINS` to the Vercel origin(s)
   - redeploy Runtime image that includes CORS support

---

## Auth / CORS rules (do not skip)

| Concern | Rule |
|---|---|
| Edge | One proxy only — Traefik. No Caddy beside it. |
| CORS | Exact allowlist via `AION_CORS_ORIGINS`. Empty = no browser CORS. |
| Tenant | `x-aion-tenant-id` enforced by Gateway — not by Vite env. |
| Operator id | `VITE_AION_OPERATOR_ID` is a **hint** for `decidedBy` display — not authority. |
| Actor / risk | Core policy on the request body — Console buttons call governed APIs only. |

---

## Done when

- [ ] `https://runtime.aionsystems.ai/health/live` and `/health/ready` return 200
- [ ] Release metadata `/` shows the deployed immutable SHA
- [ ] Runtime `:8080` is not reachable on the public host
- [ ] Deploy path is `deploy-vps.yml` (or documented SSH using the same `deploy.sh`)
- [ ] Traefik is the sole edge; Caddy not running on :443
- [ ] Console origin allowlisted **before** public Vercel traffic
- [ ] OL-001 can launch missions against the live endpoint

---

## Explicit non-goals

- Deploying Operator Console before Runtime HTTPS is verified
- Starting OL-002 / multi-client onboarding
- Adding a second reverse proxy
- Treating frontend env vars as authentication
