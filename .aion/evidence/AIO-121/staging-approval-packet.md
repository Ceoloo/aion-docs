# AIO-121 Sentinel/Forge Staging Approval Packet

- **Parent:** [AIO-121](https://linear.app/aion-empire/issue/AIO-121/p0-forge-persistent-keycloak-staging-authorization-security-sign-offs)
- **Children:** [AIO-122](https://linear.app/aion-empire/issue/AIO-122) security · [AIO-123](https://linear.app/aion-empire/issue/AIO-123) maintainer · [AIO-124](https://linear.app/aion-empire/issue/AIO-124) operator
- **Plan:** [../../../architecture/sentinel-runtime-data-trust-boundary-verification.md](../../../architecture/sentinel-runtime-data-trust-boundary-verification.md)
- **Status:** **NOT APPROVED** — human slots S1–S5 required
- **Updated:** 2026-11-10

No AI sign-off. CI Keycloak green ≠ persistent staging approval ≠ production.

## Technical evidence already available (CI / draft only)

| Artifact | Link | Classification |
|---|---|---|
| Runtime Forge audit-only bridge | [runtime#74](https://github.com/Ceoloo/aion-runtime/pull/74) | DRAFT |
| Real Keycloak interop suite | [runtime#75](https://github.com/Ceoloo/aion-runtime/pull/75) | DRAFT |
| Expanded CI Keycloak run | [actions/42413482981](https://github.com/Ceoloo/aion-runtime/actions/runs/42413482981) | CI-only |
| Sentinel bootstrap | [AION-SENTINEL#1](https://github.com/Ceoloo/AION-SENTINEL/pull/1) | DRAFT |
| Data migration 0016 | [aion-data#29](https://github.com/Ceoloo/aion-data/pull/29) | DRAFT |
| Data #30 raw pool risk | [aion-data#30](https://github.com/Ceoloo/aion-data/issues/30) | OPEN risk |
| Infra staging dispatch guard | [aion-infra#26](https://github.com/Ceoloo/aion-infra/pull/26) | OPEN — needs human merge approval |

## S1 — Operator staging target

| Field | Value |
|---|---|
| Nonprod GCP project ID | |
| Region | |
| Network / egress boundary | |
| Cost budget ceiling | |
| Synthetic tenant fixture | |
| Change window | |
| Rollback owner | |
| Decision | APPROVE TARGET / DENY |
| Operator name + time (America/New_York) | |

## S2 — Independent security reviewer (AIO-122)

| Field | Value |
|---|---|
| Reviewer name (must not be the deploying maintainer alone) | |
| SHAs reviewed (Sentinel / Data / Runtime / Infra) | |
| Findings / residuals (incl. Data #30, logout limits) | |
| Decision | APPROVE / DENY |
| Time (America/New_York) | |

## S3 — Maintainer gate (AIO-123)

| Field | Value |
|---|---|
| Maintainer name | |
| Staging Environment required reviewers confirmed | YES / NO |
| WIF SA principal (name only) | |
| Image digests + rollback digest | |
| Migrations 0015/0016 plan | |
| Keycloak issuer/realm/client (no secrets) | |
| Decision | READY / NOT READY |
| Time (America/New_York) | |

## S4 — Operator execution authorization (exact payload)

| Field | Value |
|---|---|
| Exact Runtime image ref | |
| Exact Infra workflow inputs | |
| Change ticket | AIO-121 |
| Probe plan §4.2–§4.4 acknowledged | YES / NO |
| Forge workers remain DISABLED | YES |
| Decision | AUTHORIZE RUN / DENY |
| Time (America/New_York) | |

## S5 — Final R3 operator decision

| Field | Value |
|---|---|
| Probe evidence pack link | |
| Positive + negative results | |
| Rollback drill result | |
| Decision | APPROVE STAGING TEST / DENY |
| Scope limit | Isolated staging trust-boundary only |
| Explicitly NOT authorized | Production · Forge workers · Hermes live scopes |
| Operator name + time (America/New_York) | |

## Merge / deploy order after S1–S3 (still needs S4 before apply)

1. Infra #26 (stop auto staging deploy) — only after cadence impact accepted
2. Sentinel PR → Data #29 → Runtime #74 → Runtime #75 (human merges)
3. Authorized migrator apply 0015/0016 on staging
4. Authorized staging probes with synthetic identities
5. Record S5; keep Forge workers OFF

Missing any slot ⇒ boundary remains **NOT APPROVED**.
