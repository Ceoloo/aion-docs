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

| Check | Evidence |
|---|---|
| Overlay only via `--profile twenty` | aion-infra `providers/vps/twenty/` |
| Separate DB volume (`twenty_pgdata` ≠ `aion_data`) | compose |
| Runtime→CRM on `aion_internal` (`http://twenty-server:3000`) | network + health |
| Production `.env` has `CRM_PROVIDER` unset / `ghl` | host audit |

```bash
# non-prod only
docker compose -f docker-compose.yml -f twenty/docker-compose.twenty.yml \
  --profile twenty up -d
```

## Gate B — Fake Twenty RCAOS proofs

| Check | Evidence |
|---|---|
| Runtime boots with `CRM_PROVIDER=twenty` `TWENTY_BACKEND=fake` | `/health/ready` → `crm_provider=twenty`, `crm_backend=fake` |
| AT-1…AT-7 green on twenty-fake | `npm run proof:rcaos-at-twenty` (aion-runtime) |
| No live GHL/Twenty credentials in the harness | proof-env + script unsets |

## Gate C — Live HTTP adapter (disposable workspace)

| Check | Evidence |
|---|---|
| Live Twenty maps contact / opportunity / note / task | `LiveTwentyBackend` + fixture workspace |
| Messaging / appointment writes still deferred | `TWENTY_CAPABILITY_DEFERRED` |
| Side-effect ledger `provider=twenty` | Runtime proofs |
| No production GHL tenant written | credential scope / fixture namespace |

## Gate D — Cutover gate (before production `CRM_PROVIDER=twenty`)

All must be true and recorded:

1. **Workflow** — Gate B + C lead→opportunity→approved action→Outcome on Twenty  
2. **Isolation** — tenant A cannot read/write tenant B Twenty records via Runtime  
3. **Governance** — R2 approvals + side-effect idempotency hold on Twenty  
4. **Rollback** — unset `CRM_PROVIDER` (or `=ghl`) + `GHL_BACKEND=live` restores GHL path without data loss of AION governance records  

Until D is signed off: **do not** set `CRM_PROVIDER=twenty` on production Runtime.

## Related PRs

| Slice | Repo |
|---|---|
| Compose overlay | aion-infra (Twenty profile) |
| CrmBackend + fake/live Twenty | aion-runtime |
| Doctrine | ADR-019 |
