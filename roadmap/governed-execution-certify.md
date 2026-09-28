# GOVERNED EXECUTION certify — operational checklist

Status snapshot **2026-09-28**. Implements the cash-flow priority from
[production-readiness-2026-09-28](../.aion/research/production-readiness-2026-09-28.md)
and agent mission [GE-001](../.aion/missions/GE-001-governed-execution-certify.md).

Raises Operator Loop live acceptance from “agent continues after approval” to
**GOVERNED EXECUTION — VERIFIED** (RES-002), without new subsystems.

Supersedes [production-golive.md](production-golive.md) for *current* close-out
order. Track A authenticated release work remains historical prerequisite
context.

---

## Evidence posture (current)

| Layer | Position |
|---|---|
| Documented architecture | Strong |
| Implemented platform code | Strong |
| Automated platform tests | Strong except Products `main` red |
| Verified live operation | Meaningful but release-fragmented; one contaminated CRM write |
| Proven revenue production | Not established |
| GOVERNED EXECUTION — VERIFIED | **Open** |

---

## Action 1 — Certify one clean AION Systems revenue workflow

Ordered gates. Do not skip ahead to a live write until 1.1–1.4 are closed.

### 1.1 Unblock Products releasability

- [x] Merge Products [#44](https://github.com/Ceoloo/aion-products/pull/44)
      — merged 2026-09-28T21:45:04Z as `06eedbf9a889ef2c0c721f102baf8c255a36221c`
- [x] Confirm Products `main` CI green after merge
      — [CI run 36488161210](https://github.com/Ceoloo/aion-products/actions/runs/36488161210) success
      (typecheck, web console, WCC, synthetic eval, COPILOT_IMAGE)
- [x] Record Products SHA in the release manifest
      — `releases/governed-execution-candidate.manifest.json` (`result` still UNVERIFIED)

### 1.2 Deploy Runtime tip including synthetic boundary

**Owner:** authorized production maintainer (Infra `deploy-vps` + production
Environment reviewer). Cloud agents and read-only GitHub tokens hit HTTP 403 on
workflow_dispatch — that is an access boundary; **do not work around it**.

Candidate image (CI-published for Runtime `#57` / `20fe3a4`):

```text
ghcr.io/ceoloo/aion-runtime@sha256:f7041f4123929b8f36de9160e82dd6d350edb949d40bcdaa4d0d6538f53694fc
git_sha: 20fe3a435dab5f12175ae03998ad709398ce931d
```

Observed serving before this deploy (2026-09-28): `/health/ready` →
`git_sha=49cff4e3bd1e48ed8a43414c8191ff30a28d00be` (behind `#57`).

Maintainer sequence:

1. Review candidate digest against migration set and previous-image × new-schema
   rollback compatibility.
2. From **Infra `main`**, dispatch [`deploy-vps`](https://github.com/Ceoloo/aion-infra/blob/main/.github/workflows/deploy-vps.yml)
   and obtain the **production** Environment review:

```bash
gh workflow run deploy-vps.yml --repo Ceoloo/aion-infra   --ref main   -f environment=production   -f runtime_image='ghcr.io/ceoloo/aion-runtime@sha256:f7041f4123929b8f36de9160e82dd6d350edb949d40bcdaa4d0d6538f53694fc'   -f git_sha='20fe3a435dab5f12175ae03998ad709398ce931d'
```

3. After the workflow completes, capture into
   `releases/governed-execution-candidate.manifest.json` (keep `result: UNVERIFIED`
   until live GE-001 PASS):

| Capture | Source |
|---|---|
| Workflow run URL + conclusion | GitHub Actions `deploy-vps` |
| Serving image digest | deploy.sh / workflow summary |
| Prior rollback digest | deploy.sh (“previous runtime”) / `docker inspect` before roll |
| `/health/ready` `git_sha` | Must equal `20fe3a4…` |

Checklist:

- [x] Build/boot-certify Runtime image including [#57](https://github.com/Ceoloo/aion-runtime/pull/57)
      — CI run [36385472906](https://github.com/Ceoloo/aion-runtime/actions/runs/36385472906); digest `f7041f41…`
- [ ] Maintainer reviews candidate digest + migration/rollback compatibility
- [ ] Dispatch `deploy-vps` from Infra `main` + production Environment approval
- [ ] Workflow result recorded (URL + conclusion)
- [ ] Serving digest recorded
- [ ] Prior rollback digest recorded
- [ ] Verify `GET /health/ready` `git_sha` == `20fe3a435dab5f12175ae03998ad709398ce931d`

> **Gate:** do not start §1.3 until §1.2 serving identity is verified.
> **Gate:** do not start §1.4 (GE-001) until §1.3 cleanup is recorded.
> Existing Annfiera payment stays outside GE-001 attributed cash. Provider
> expense stays `UNVERIFIED` unless backed by actual priced charges.

### 1.3 Close the contamination incident (CRM cleanup)

Follow
[`aion-runtime` incident](https://github.com/Ceoloo/aion-runtime/blob/main/docs/incidents/2026-09-27-ol001-synthetic-crm-write.md):

- [ ] Delete **only** the identified synthetic Annfiera note
- [ ] Append provider note id, UTC time, operator identity, contact id as SHA-256
- [ ] Leave OL-001 mission / executions / approvals / side-effect ledger intact
- [ ] Do **not** touch or re-attribute any existing Annfiera payment — it
      predates GE-001 and stays outside this acceptance run’s attributed outcome

### 1.4 Live GOVERNED EXECUTION acceptance (fresh record)

Use Operator Console +
[`operator-loop-v1`](https://github.com/Ceoloo/aion-runtime/blob/main/docs/operator-loop-v1.md)
procedure on a **fresh low-risk real record** — never synthetic evidence on a
client contact.

Pass bar:

| # | Requirement | Evidence |
|---|---|---|
| 1 | Explicit authority envelope (principal + agent + caps/resources + risk max + spend/time + expires) | Mission + grant records |
| 2 | Task-scoped capabilities only | Agent registry + authorization audit |
| 3 | Agent proposes real GHL operation | Proposal payload |
| 4 | Gateway → AUTO \| ASK \| DENY | Decision audit |
| 5 | ASK: named human decides; DENY: no side effect | Approval / rejection + side-effect absence |
| 6 | Approved execution exactly once | Idempotency key + single side-effect row |
| 7 | Trace + receipt (lineage + external IDs) | Execution tree |
| 8 | Restart/resume under same `rootExecutionId` | Deliberate restart at governed pause |
| 9 | Provider reconciliation before retry after crash-after-provider-success | Operator note + no duplicate write |
| 10 | Eval scores ≥1 governed execution | EvaluationResult |
| 11 | Economics with **separate** fields: provider expense, operator time, pipeline value, collected cash — see pricing rule below | Priced source **or** explicit UNVERIFIED stamp + outcome fields |
| 12 | Durable evidence pack | Single acceptance record |

#### Economics / pricing rule (row 11)

Runtime today records **abstract cost units**, not priced USD expense
([operator-loop-v1](https://github.com/Ceoloo/aion-runtime/blob/main/docs/operator-loop-v1.md):
“a USD cost or financial ROI needs a priced ledger before it can be claimed”).

Therefore for GE-001:

- **Provider expense** must be either (a) actual charges from a priced source
  (provider invoice, billing export, or priced ledger tied to this run), **or**
  (b) stamped explicitly `UNVERIFIED` in the acceptance pack.
- A completed mission plus a Runtime `totalCostUnits` total **cannot** certify
  economic execution and must not be reported as provider expense.
- **Pipeline value** and **collected cash** attribute only to this GE-001 run.
  Any existing Annfiera payment predates the run and remains separate.

- [ ] Approval path PASS
- [ ] Denial-without-side-effect path PASS
- [ ] Restart/resume PASS
- [ ] Provider reconciliation PASS
- [ ] Terminal outcome + economics PASS (provider expense priced **or** UNVERIFIED)
- [ ] Contaminated OL-001 explicitly excluded from Mission-001 counts
- [ ] Pre-existing Annfiera payment excluded from GE-001 attributed outcome

---

## Action 2 — Lock the release and recovery boundary

- [ ] Publish filled
      [`releases/governed-execution-candidate.manifest.json`](../releases/governed-execution-candidate.manifest.json)
      (tips below are **candidates only** until production identity verified)
- [ ] Align Runtime Core/Data pins with the certified tips (today Runtime still
      defaults to Core `52ecf40` / Data `5da145a` while `main` tips are ahead)
- [ ] Audit / backfill NULL tenant rows on production
- [ ] Verify RLS with the application role on production
- [ ] Enable CA-verified database TLS (`rejectUnauthorized: true` + CA)
- [ ] Test previous Runtime image against newly migrated schema
- [ ] Reconcile DR drill results log + recovery kit stamps with claimed
      RECOVERABLE — VERIFIED (dated results, RTO/RPO, interventions)

---

## Action 3 — Authenticated durable Revenue Copilot → Mission-001 dataset

**Do not expose Copilot to clients until all of the following land.**

- [ ] Authenticated caller identity on Copilot session endpoints
- [ ] Tenant binding + authorization
- [ ] Throttling
- [ ] Session ownership checks
- [ ] Checkpoint every accepted turn and feedback event
- [ ] Reconstruct session after forced restart
- [ ] Begin ≥25 real AION Systems conversations with manual ground truth and
      complete outcome lineage

Mission-001 gates remain **not found** (evidence gaps):

| Gate | Status |
|---|---|
| ≥25 real evaluable conversations | Not found |
| ≥85% explicit-fact accuracy | Not found |
| ≥60% useful/acted-on rated interventions | Not found |
| ≥10 positive conversion events | Not found |
| ≥3 meaningful downstream conversions | Not found |
| Complete production lineage | Not found |

---

## Candidate tips (2026-09-28 — not production-certified)

| Component | Tip | Caveat |
|---|---|---|
| Runtime git | `20fe3a4` | Includes #57; **deploy not evidenced** |
| Last known prod Runtime | `10de0663` / digest `e3fef42f…` | Sep 22 Infra evidence |
| Core `main` | `26f43e5` | Ahead of Runtime pin |
| Data `main` | `cb29e7c` | Migrations `0001`–`0013`; ahead of Runtime pin |
| Products `main` | `41a8749` | CI red until #44 |
| Products fix branch | #44 | CI green / MERGEABLE |
| Infra `main` | `3382b61` | — |

---

## Operating posture until PASS

- Supervised, named-human execution only
- Digital Empire: leads/distribution only — no autonomous production workflows
- G-Star / Synapse / Personal Ops: no change (incubate / paused / isolated)
- Defer: Rooms/Desks expansion, OTEL, Okta, Skills registry, autonomous
  messaging/appointments, broad client onboarding
