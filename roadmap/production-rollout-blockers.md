# Production rollout — remaining blockers

Status snapshot for closing the live Revenue OS surface. This is **ops
rollout**, not OL-001 mission credit.

## Verified now

| Surface | Status |
|---|---|
| Production Runtime health | ✅ `https://runtime.srv1655818.hstgr.cloud/health/ready` (`aion-runtime`, DB up, `git_sha=3329c01…`) |
| Preferred DNS `runtime.aionsystems.ai` | ❌ does not resolve from this agent — keep clients on Hostinger FQDN until Traefik DNS is cut over |
| GHL live capability (reads/writes/replay/R2) | ✅ proven earlier; model gate still needs agent `OPENROUTER_API_KEY` for harness |

## Blockers

### 1. Revenue production `/api` → Runtime

| Client | Problem | Fix |
|---|---|---|
| Vercel `ceoloo-aion-revenue-copilot` | Linked to **`AION-Sys/Ceoloo-aion-revenue-copilot`** (Next.js AI Gateway), **not** `Ceoloo/aion-products`. Uses `AION_AI_GATEWAY_*`, not Runtime `/v1`. | Either re-point production Revenue to VPS `revenue-copilot` profile **or** rebuild the Vercel app on `aion-products` with `AION_RUNTIME_URL`. |
| VPS `revenue-copilot` profile | Not running; compose previously omitted `AION_RUNTIME_URL`. | Set `COPILOT_IMAGE` + `OPENROUTER_*` (done) + `AION_RUNTIME_URL`, then `docker compose --profile revenue-copilot up -d`. Infra PR wires `AION_RUNTIME_URL`. |

### 2. Runtime / backend deployment access

Agent can **read** Runtime health over HTTPS. It **cannot** SSH to the VPS,
edit `/opt/aion/.env`, or roll compose. Operator must apply infra changes and
bring up Copilot on the host.

CORS today allowlists `https://aion-operator-console.vercel.app` — keep that
origin (and any new Workforce Control alias) in `AION_CORS_ORIGINS`.

### 3. Durable session storage (merge conflicts)

`aion-data` PR [#1](https://github.com/Ceoloo/aion-data/pull/1)
(`codex/revenue-session-persistence`) was **CONFLICTING** vs `main`
(migration number collision `0002`, export / test-table lists).

Resolution branch: `cursor/revenue-session-persistence-6743` —
renumber to `0009_revenue_sessions.sql`, merge exports + `ALL_TABLES`, wire
`revenueSessions` on `DataLayer`.

Still required after merge: run migrations on production Postgres (app role
`SELECT/INSERT/UPDATE` on `revenue_sessions`), then wire products SessionStore
to `PostgresRevenueSessionRepository`.

### 4. Operator + Growth deployments

| Project | Status | Fix |
|---|---|---|
| `aion-operator-console` | Latest production deploy **ERROR** — install cmd is Revenue Copilot (`npm run setup:core && … web ci`), not Workforce Control. | Root `vercel.json` in `aion-products` builds `workforce-control/`. Redeploy after merge. Env: `VITE_AION_RUNTIME_URL=https://runtime.srv1655818.hstgr.cloud`. |
| `v0-ai-growth-operator` | `link: null`, **0 deployments**, no domains. | **Source mapping missing** — no GitHub repo attached. Provide the Growth source repo (or confirm retirement) before any deploy. Not `aion-desks` (Stripe landing only). |

## Ordered close-out

1. Merge session-persistence data PR → migrate prod DB.
2. Merge products `vercel.json` → Operator Console redeploy green.
3. On VPS: Copilot profile up with `AION_RUNTIME_URL` → verify `/health/ready` on `copilot.*`.
4. Map or retire Growth Vercel project.
5. Cut DNS `runtime.aionsystems.ai` (optional) once Traefik host is ready.
6. Then resume OL-001 model gate / Mission 001 — not before `/api` and Operator talk to live Runtime.
