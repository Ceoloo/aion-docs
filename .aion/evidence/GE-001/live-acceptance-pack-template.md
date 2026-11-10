# GE-001 Live Acceptance Pack Template

- **Linear:** [AIO-37](https://linear.app/aion-empire/issue/AIO-37/p0-certify-ge-001-on-a-fresh-low-risk-real-record)
- **Checklist:** [../../../roadmap/governed-execution-certify.md](../../../roadmap/governed-execution-certify.md)
- **Mission:** [../../missions/GE-001-governed-execution-certify.md](../../missions/GE-001-governed-execution-certify.md)
- **Status:** Template only — result remains **UNVERIFIED** until live fields filled
- **Rule:** Local/fake RCAOS AT-1…AT-7 **do not** certify GE-001

## Preconditions (must be true first)

| Precondition | Status |
|---|---|
| CRM-001 Gates A–D complete + Twenty-capable Runtime production identity ([AIO-109](https://linear.app/aion-empire/issue/AIO-109)) | OPEN |
| Docs re-scope PR [#107](https://github.com/Ceoloo/aion-docs/pull/107) reviewed (Twenty target) | OPEN |
| Named human approver available | |
| Fresh low-risk **real** Twenty record selected (not synthetic-on-client) | |
| Release manifest `result` still UNVERIFIED until PASS/FAIL | |

## Run identity

| Field | Value |
|---|---|
| Operator | |
| Approver | |
| Start (America/New_York) | |
| CRM provider | twenty (required after CRM-001) |
| Record refs (ids only; no PII dump) | |
| rootExecutionId | |
| Runtime serving digest | |
| `/health/ready` git_sha | |
| Policy / approval versions | |

## Acceptance matrix

| # | Criterion | Evidence links | Pass? |
|---|---|---|---|
| 1 | Authority envelope + task-scoped permissions | | |
| 2 | Proposal → AUTO/ASK/DENY audit trail | | |
| 3 | Approved effect recorded | | |
| 4 | Denial path with **zero** side effect | | |
| 5 | External IDs / lineage | | |
| 6 | Restart/resume same `rootExecutionId` | | |
| 7 | Provider reconciliation before retry after ambiguous success | | |
| 8 | Evaluation / terminal outcome | | |
| 9 | Durable evidence pack filed | | |

## Economics (separate fields)

| Field | Value | Source | Verification |
|---|---|---|---|
| Provider expense (USD) | | invoice / billing export / priced ledger | VERIFIED / **UNVERIFIED** |
| Operator time | | | |
| Pipeline value (this run only) | | | |
| Collected cash (this run only) | | must not use pre-existing payments | |
| Runtime cost units | | informational only | not USD |

`totalCostUnits` is **not** USD expense.

## Final verdict

| Field | Value |
|---|---|
| Result | PASS / FAIL / BLOCKED |
| Named certifier | |
| Timestamp (America/New_York) | |
| Manifest update PR | |
| Notes / residual risks | |

**Do not** mark GOVERNED EXECUTION — VERIFIED without PASS above and updated
`releases/governed-execution-candidate.manifest.json`.
