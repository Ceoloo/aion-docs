# CRM-001 cutover sequence

**Status:** Active after ADR-019 + compose/abstraction PRs  
**Mission:** [.aion/missions/CRM-001-twenty-integration-benchmark.md](../.aion/missions/CRM-001-twenty-integration-benchmark.md)

Ordered gates. **Production GHL stays unchanged until Gate D passes.**

```text
A  Enable Twenty on a non-prod host
      → B  Fake Twenty RCAOS proofs
           → C  Live HTTP adapter against disposable Twenty
                → D  Cutover gate (isolation + governance + rollback)
                     → only then CRM_PROVIDER=twenty in production
```

## Gate A — Non-prod Twenty host

**Blocked until an isolated staging host exists.** The live production Docker
daemon (`Aionempire`) already runs Runtime, PostgreSQL, and Traefik — do **not**
run Gate A there. Production CRM (GHL) and production `.env` stay unchanged;
cutover remains unauthorized until Gate D.

### Staging host prerequisites (before any Gate A command)

Choose a **separate non-production host** with its own Docker daemon and
**isolated** Postgres volume (`twenty_pgdata` ≠ production `aion_data`).

Configure staging access only through the host secret store / GitHub Environment
`staging` secrets — **never paste secret values into chat, tickets, or PRs.**

| Config (names only) | Where | Notes |
|---|---|---|
| `TWENTY_PG_PASSWORD` | staging host `.env` (Twenty overlay) | Required by compose; Twenty DB only |
| `TWENTY_APP_SECRET` | staging host `.env` (Twenty overlay) | Required by compose |
| `TWENTY_API_BASE_URL` | staging Runtime env | Prefer `http://twenty-server:3000` on `aion_internal` |
| `TWENTY_API_KEY` | staging Runtime env | Staging workspace key only |
| `CRM_PROVIDER` / `TWENTY_BACKEND` | staging Runtime only | Keep production unset / `ghl` |
| Staging Runtime `DATABASE_URL` (or equivalent) | staging Runtime | Staging DB — not production |

GitHub Environment `staging` may exist without secrets yet; populate staging-only
values on the staging host (and optionally mirror names into that Environment)
before enabling the overlay.

### Gate A checks

| Check | Evidence |
|---|---|
| Target is **not** the production Docker host | host inventory / operator attestation |
| Overlay only via `--profile twenty` | aion-infra `providers/vps/twenty/` |
| Separate DB volume (`twenty_pgdata` ≠ `aion_data`) | compose on staging host |
| Runtime→CRM on `aion_internal` (`http://twenty-server:3000`) | network + health |
| Production `.env` has `CRM_PROVIDER` unset / `ghl` | production host audit (read-only) |
| Staging secrets present; production secrets unused | staging secret store audit |

```bash
# staging host only — never on Aionempire / production
docker compose -f docker-compose.yml -f twenty/docker-compose.twenty.yml \
  --profile twenty up -d
```

## Gate B — Fake Twenty RCAOS proofs

| Check | Evidence |
|---|---|
| Runtime boots with `CRM_PROVIDER=twenty` `TWENTY_BACKEND=fake` | `/health/ready` → `crm_provider=twenty`, `crm_backend=fake` |
| AT-1…AT-7 green on twenty-fake | `npm run proof:rcaos-at-twenty` (aion-runtime) |
| No live GHL/Twenty credentials in the harness | proof-env + script unsets |

### Gate B evidence — **PASS** (2026-10-27)

| Item | Record |
|---|---|
| PR | [Ceoloo/aion-runtime#67](https://github.com/Ceoloo/aion-runtime/pull/67) — merged |
| Merge commit | `084dea5` on `main` |
| Scope | Make CRM-001 Gate B pass; extend `SYNTHETIC_TO_PRODUCTION_DENIED` to `twenty-live` |
| CI | [actions/runs/40446107527](https://github.com/Ceoloo/aion-runtime/actions/runs/40446107527) successful |
| Production impact | **None** — do not set `CRM_PROVIDER=twenty` in prod |

## Gate C — Live HTTP adapter (disposable workspace)

Do not trust Gate C until these adapter invariants hold against Twenty **v0.40.0**:

| Invariant | Why |
|---|---|
| Create responses require a real Twenty `id` (no synthetic substitutes) | Ledger / idempotency must bind to CRM ids |
| `note.create` sends `body` (not `bodyV2`) | v0.40 Note model field |
| Notes/tasks with `contactId` / `opportunityId` / `companyId` create `noteTargets` / `taskTargets` | Relations are separate objects in v0.40 |
| Tenant isolation proven with **two** staging tenant contexts | Static review cannot certify isolation |

| Check | Evidence |
|---|---|
| Live Twenty maps contact / opportunity / note / task | `LiveTwentyBackend` + fixture workspace |
| Note/task record links via NoteTarget / TaskTarget | HTTP traces on disposable workspace |
| Messaging / appointment writes still deferred | `TWENTY_CAPABILITY_DEFERRED` |
| Side-effect ledger `provider=twenty` | Runtime proofs |
| No production GHL tenant written | credential scope / fixture namespace |
| Cross-tenant read/write denied | two staging API keys / workspaces |

## Gate D — Cutover gate (before production `CRM_PROVIDER=twenty`)

All must be true and recorded:

1. **Workflow** — Gate B + C lead→opportunity→approved action→Outcome on Twenty  
2. **Isolation** — tenant A cannot read/write tenant B Twenty records via Runtime  
3. **Governance** — R2 approvals + side-effect idempotency hold on Twenty  
4. **Rollback** — unset `CRM_PROVIDER` (or `=ghl`) + `GHL_BACKEND=live` restores GHL path without data loss of AION governance records  

Until D is signed off: **do not** set `CRM_PROVIDER=twenty` on production Runtime.

### Gate D sign-off checklist (template)

Copy into the cutover evidence record. Leave blank until exercised; do **not**
treat this template as authorization.

| Field | Required evidence | Recorded by | Date | Pass? |
|---|---|---|---|---|
| **Workflow** | Gate B evidence link + Gate C disposable evidence path (contact→opp→approved action→Outcome on Twenty); Runtime `/health/ready` shows `crm_provider=twenty` on staging only | | | |
| **Isolation** | Two staging tenant contexts; tenant A cannot read/write tenant B Twenty records via Runtime (two-process or `TWENTY_API_KEY_B`); no production GHL/Twenty writes | | | |
| **Governance** | R2 approval park/resume on Twenty; side-effect ledger idempotency (one provider object per key); `SYNTHETIC_TO_PRODUCTION_DENIED` on protected Twenty hash | | | |
| **Rollback** | Staging drill: unset `CRM_PROVIDER` (or `=ghl`) + `GHL_BACKEND=live` restores GHL path; AION governance rows intact; prior Runtime digest compatibility reviewed (see aion-infra#20 — do not enable prod rollback fields until qualified) | | | |
| **Sign-off** | Maintainer names (Integrations + Security + Runtime) + explicit “authorize prod `CRM_PROVIDER=twenty`” | | | |

## Related PRs

| Slice | Repo |
|---|---|
| Compose overlay | aion-infra #21 (merged) |
| CrmBackend + fake/live Twenty | aion-runtime #64 (merged) |
| Cutover sequence A→D | aion-docs #102 (merged) |
| Gate B PASS + twenty-live synthetic guard | aion-runtime #67 (merged) |
| Doctrine | ADR-019 (#100/#101) |
| Migration-aware rollback (draft) | aion-infra #20 (open draft — do not enable prod rollback fields) |
| Gate A staging host prereqs + Gate C disposable proof | follow-up docs / runtime / infra PRs |
