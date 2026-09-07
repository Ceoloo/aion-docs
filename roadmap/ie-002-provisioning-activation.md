# IE-002 — Provisioning Readiness + Activation Gate

**Turn IE-001’s visible blockers into governed state transitions.**

IE-001 established: a client can be commercially accepted, qualified, and
architected **before** technical activation prerequisites exist.

IE-002 makes those prerequisites explicit, evidential, and gated:

```text
blueprint_approved
      ↓
provisioning
      ├─ agreement + payment condition
      ├─ workspace + tenant
      ├─ GHL connected
      ├─ model provider configured
      ├─ data sources
      ├─ permissions validated
      ├─ baseline captured
      └─ workflow package provisioned
      ↓
activation_ready
      ↓
HUMAN APPROVAL
      ↓
active  →  feeder into OL-001 Mission 001
```

Platform baseline remains `execution-platform-v0.2.0` (+ bootable Runtime tags).
This is still **not** a second runtime.

---

## Doctrine

| Rule | Meaning |
|---|---|
| Commercial ≠ technical | Paid/signed does not imply `active` |
| No invisible manual work | Every verified step records `completedBy` + `evidence` |
| No auto-activation | Checklist completion → `activation_ready` only; human activates |
| Retry is resumable | Failed/blocked steps can reopen; never duplicate tenant/workspace/workflow |
| GHL + model are P0 gates | They converge with OL-001’s pause reasons |

---

## APIs (tenant header required)

| Method | Path | Effect |
|---|---|---|
| POST | `/v1/implementations/:caseId/provisioning/start` | `blueprint_approved` → `provisioning` |
| POST | `/v1/implementations/:caseId/provisioning/steps/:key` | Update step status + evidence |
| POST | `/v1/implementations/:caseId/activation/ready` | All required steps verified → `activation_ready` |
| POST | `/v1/implementations/:caseId/activate` | `{ approvedBy }` → `active` |

Optional readiness probes (config presence / connection evidence — **not**
autonomous provisioning):

| Method | Path | Effect |
|---|---|---|
| POST | `/v1/implementations/:caseId/provisioning/steps/ghl_connection/probe` | Record GHL readiness evidence; verify when ok |
| POST | `/v1/implementations/:caseId/provisioning/steps/model_access/probe` | Record model-provider config evidence; verify when ok |

---

## Required steps for `activation_ready`

`agreement_payment`, `workspace_tenant`, `ghl_connection`, `model_access`,
`data_sources`, `permissions`, `baseline`, `workflow_activation`.

---

## Console

On `/implementations/:caseId` after blueprint approval:

- Start provisioning
- Verify / fail / re-block each step with evidence
- Mark activation ready
- Activate (human approval)

---

## Relation to OL-001

OL-001 is paused on **model access + GHL**. IE-002 is the same workstream:

1. Flip `ghl_connection` and `model_access` from `blocked` → `verified`
2. Pass activation gate → `active`
3. IE case becomes the feeder into OL-001 Mission 001 (first approved live workflow)

Provisioning tasks still **do not** count toward OL-001’s 100 real revenue missions.

---

## Acceptance matrix (IE-002 closed when these pass in CI)

| Case | Expected |
|---|---|
| Start provisioning before blueprint approval | Reject |
| Verify step without evidence | Reject |
| Verify step without `completedBy` | Reject |
| Reach ready with missing required step | Reject |
| Verify all required steps | `activation_ready` |
| Activate without `approvedBy` | `400 approved_by_required` |
| Activate with approval | `active` |
| Re-activate already active case | Idempotent |
| Tenant A touches Tenant B case | Reject |
| Commercial status change alone | Never causes activation |

Runtime CI: `npm run proof:ie002` (HTTP matrix). Core unit tests cover the
same transition rules.

---

## Probe maturity (keep narrow)

Current:

```text
probe → configuration exists → evidence recorded → step verified
```

Not:

```text
probe → provision account → modify CRM → enable model → activate workflow
```

Later (AIO-16 model provider, AIO-17 GHL adapter), upgrade the **same** probe
interfaces from `CONFIG_PRESENT` to `AUTHENTICATED` / `CAPABILITY_VERIFIED` /
`TENANT_SCOPED` / `WRITE_TEST_PASSED` without changing the IE state model.

---

## Explicit non-goals / next work

**Do not start IE-003 next.** After this PR chain lands green:

```text
AIO-16  Model provider access
AIO-17  GHL adapter
        ↓
upgrade probes from config-present → real capability verification
        ↓
first real implementation case → activation_ready → human activation
        ↓
OL-001 Mission 001
```

Bridge:

> Sale → Intake → Recommendation → Blueprint → Provisioning → Human Activation → Revenue Mission
