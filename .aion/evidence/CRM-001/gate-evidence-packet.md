# CRM-001 Gate Evidence Packet

- **Linear:** [AIO-109](https://linear.app/aion-empire/issue/AIO-109/p0-crm-001-twenty-cutover-gates-a-d-and-twenty-capable-runtime)
- **Sequence:** [../../../roadmap/crm-001-cutover-sequence.md](../../../roadmap/crm-001-cutover-sequence.md)
- **Status:** Operator fill-in — **cutover NOT complete** until Gate D signed
- **Updated:** 2026-11-10

Agents may prepare this packet. Only named humans may attest Gate A host
isolation, Gate C live disposable proof, Gate D sign-off, and production pin.

## Gate B — recorded PASS (code/docs)

| Item | Evidence |
|---|---|
| Runtime Gate B | [aion-runtime#67](https://github.com/Ceoloo/aion-runtime/pull/67) merged |
| Docs evidence PR | [aion-docs#111](https://github.com/Ceoloo/aion-docs/pull/111) (merge independently) |
| Fake proofs | `npm run proof:rcaos-at-twenty` (local/CI — not production) |

## Gate A — isolated staging host (MISSING until filled)

| Check | Attestor | Evidence link / note | Pass? |
|---|---|---|---|
| Host is **not** production `Aionempire` Docker daemon | Maintainer | | |
| Separate Postgres volume `twenty_pgdata` | Maintainer | | |
| Staging-only secrets (names only in tickets) | Maintainer | | |
| Overlay `--profile twenty` up | Maintainer | | |
| Production `CRM_PROVIDER` still unset/`ghl` | Maintainer | | |
| Runtime staging `/health/ready` shows twenty path as intended | Maintainer | | |

**Attestor:** _________________ **Time (America/New_York):** _________________

## Gate C — disposable live Twenty (MISSING until filled)

Scaffolding: [aion-runtime#69](https://github.com/Ceoloo/aion-runtime/pull/69) (review/merge separately).

| Check | Attestor | Evidence | Pass? |
|---|---|---|---|
| Disposable Twenty **v0.40** workspace | Operator | | |
| Real ids on create (no synthetic substitutes) | Operator | | |
| Notes use `body` (not `bodyV2`) | Operator | | |
| NoteTarget / TaskTarget links | Operator | | |
| Two-tenant cross-tenant denial | Operator | | |
| Protected-hash / synthetic write refused on Twenty live | Operator | | |
| Side-effect ledger `provider=twenty` | Operator | | |
| Messaging/appointments still deferred | Operator | | |

**Attestor:** _________________ **Time (America/New_York):** _________________

## Gate D — cutover sign-off (MISSING until filled)

| Criterion | Evidence | Signer |
|---|---|---|
| Workflow B+C lead→opportunity→approved action→Outcome | | |
| Isolation tenant A ↛ B | | |
| Governance R2 approvals + idempotency | | |
| Rollback to GHL path without losing AION governance records | | |
| Runtime candidate digest chosen (≥ Twenty backend) | | |
| Infra deploy pin + production Environment approval | | |
| Production `/health/ready` `crm_provider=twenty` + serving digest | | |
| Prior rollback digest recorded | | |

**Gate D sign-off (all required):**

| Role | Name | APPROVE/DENY | Timestamp (America/New_York) |
|---|---|---|---|
| Maintainer | | | |
| Independent operator / Environment reviewer | | | |
| Architecture (optional) | | | |

Until complete: **do not** set production `CRM_PROVIDER=twenty`.
HTTP 403 on deploy dispatch is an access boundary — do not bypass.
