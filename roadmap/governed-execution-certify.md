# GOVERNED EXECUTION certify — operational checklist

Status snapshot **2026-10-26** (checklist gates unchanged; Runtime #57 deployment identity reverified).
Implements the cash-flow priority from
[production-readiness-2026-10-12](../.aion/research/production-readiness-2026-10-12.md)
(prior: [2026-09-28](../.aion/research/production-readiness-2026-09-28.md))
and agent mission [GE-001](../.aion/missions/GE-001-governed-execution-certify.md).

**Do not retarget** the candidate pin to Runtime tip (`7ad6649` / #59–#62).
Certify the existing `20fe3a4` digest first.

Raises Operator Loop live acceptance from “agent continues after approval” to
**GOVERNED EXECUTION — VERIFIED** (RES-002), without new subsystems.

Supersedes [production-golive.md](production-golive.md) for *current* close-out
order. Track A authenticated release work remains historical prerequisite
context.

---

## Evidence posture (current)

| Layer | Position |
|---|---|
| Documented architecture | Strong, increasingly ahead of operations |
| Implemented platform code | Strong on current branches (registry, assurance, rooms, RCAOS tip) |
| Automated / synthetic tests | Strong; RCAOS AT-1…AT-7 green on fake harness |
| Verified live operation | Runtime #57 is serving; isolated rollback/RLS probe found previous-image tenant access incompatibility if RLS is active; GE-001 live acceptance remains outstanding |
| Proven revenue production | Not established |
| GOVERNED EXECUTION — VERIFIED | **Open** (candidate still `20fe3a4`, not tip) |

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
The authorized deploy-vps run and serving identity were recorded in the
[candidate manifest](../releases/governed-execution-candidate.manifest.json).
On 2026-10-26, a fresh `GET /health/ready` returned `status=ready`,
`database=up`, `environment=production`, and the exact candidate
`git_sha=20fe3a435dab5f12175ae03998ad709398ce931d`. The deploy workflow log
also records the candidate digest and prior image digest. It reports 2 of 13
migrations applied but does not name them, so schema/rollback compatibility
remains unverified.

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
- [x] Dispatch `deploy-vps` from Infra `main` + production Environment approval
      — [run 36758112318](https://github.com/Ceoloo/aion-infra/actions/runs/36758112318), success; production reviewer recorded in manifest
- [x] Workflow result recorded (URL + conclusion)
- [x] Serving digest recorded — `sha256:f7041f4123929b8f36de9160e82dd6d350edb949d40bcdaa4d0d6538f53694fc`
- [x] Prior rollback digest recorded — `sha256:b12043ad9be4937dc00345ffe973d61120921492c76b4d5806fa6a7e99eda5ee` (`75e9acd`)
- [x] Verify `GET /health/ready` `git_sha` == `20fe3a435dab5f12175ae03998ad709398ce931d`
      — rechecked 2026-10-26; `status=ready`, database `up`
- [ ] Capture exact applied migration IDs and prove previous-image behavior against the resulting schema, or verify a rollback mitigation

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

The 2026-10-26 isolated previous-image/RLS probe is recorded in
[`releases/ge001-closeout-2026-10-26.md`](../releases/ge001-closeout-2026-10-26.md).
It is not a production verification: the production migration ledger remains
uncaptured, and the candidate image's database client sets
`rejectUnauthorized: false`.

---

## Action 3 — Authenticated durable Revenue Copilot → Mission-001 dataset

**Do not expose Copilot to clients until all of the following land.**

Draft progress (does **not** clear client exposure or GE-001): Products
[#45](https://github.com/Ceoloo/aion-products/pull/45) (draft) persists active
turns, feedback, state, and execution lineage through Runtime; restart restores
committed turns without replaying earlier AI work; interrupted operations return
`409 reconciliation_required`. Local: typecheck + 82 tests (incl. service
restart). PR CI green
([run 36491343366](https://github.com/Ceoloo/aion-products/actions/runs/36491343366)).
Still draft for review of checkpointed customer data and interrupted finish
behavior. **Does not** establish exactly-once provider execution, client-facing
Copilot authentication, production deploy of this change, or GE-001 PASS.

- [ ] Authenticated caller identity on Copilot session endpoints
- [ ] Tenant binding + authorization
- [ ] Throttling
- [ ] Session ownership checks
- [ ] Checkpoint every accepted turn and feedback event
      — draft implementation in Products #45 (test-env recovery of committed turns)
- [ ] Reconstruct session after forced restart
      — draft implementation in Products #45 (service restart test + CI)
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

## Candidate pins (2026-10-20; GE-001 still open)

| Component | Tip | Caveat |
|---|---|---|
| Runtime git | `20fe3a4` | Includes #57; serving identity verified; rollback compatibility not cleared |
| Previous Runtime image | `75e9acd` / digest `b12043ad…` | Previous-image compatibility with the migrated schema is not proven |
| Core `main` | `6ba12ace` | Runtime setup pin remains `52ecf40` |
| Data `main` | `9d4b59f` | Migrations `0001`–`0014`; Runtime setup pin remains `5da145a`; deployment log says 2 of 13 applied without naming them |
| Products `main` | `06eedbf9` | #44 merged; main CI green (run 36488161210) |
| Infra deploy workflow | `3382b611` | Run 36758112318 succeeded under production approval |

---

## Operating posture until PASS

- Supervised, named-human execution only
- Digital Empire: leads/distribution only — no autonomous production workflows
- G-Star / Synapse / Personal Ops: no change (incubate / paused / isolated)
- Defer: Rooms/Desks expansion, OTEL, Okta, Skills registry, autonomous
  messaging/appointments, broad client onboarding
