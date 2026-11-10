# AION Portfolio Registry

- **Status:** Active operating registry (v0.1) — Holding review required for status changes
- **Charter:** [../strategy/aion-universe-ecosystem-charter.md](../strategy/aion-universe-ecosystem-charter.md)
- **Linear:** [AIO-88](https://linear.app/aion-empire/issue/AIO-88/aion-universe-ratify-ecosystem-charter-and-establish-product-admission)
- **Companion IC process:** [investment-committee.md](investment-committee.md)
- **Captured:** 2026-11-10 (America/New_York)

One accountable owner, one canonical repo (or explicit “multi”), one validated
state, one portfolio status, and one user-value goal per entry. Registry rows
are **not** production claims, funding approvals, or Workspace “available”
labels.

## Status vocabulary

| Status | Meaning |
|---|---|
| **GROW** | Funded operating priority; protects cash flow and production reliability |
| **VALIDATE** | Active customer/user proof required before broader spend |
| **INCUBATE** | Bounded experiment with owner, budget ceiling, and kill condition |
| **PAUSE** | No active delivery spend; research notes only |
| **PROTECTED** | Capacity reserved; not a product backlog |

## Current registry

| ID | Entry | Owner | Canonical repo / surface | Validated state | Status | User-value goal |
|---|---|---|---|---|---|---|
| PR-SYS | AION Systems (platform + commercial delivery) | AION Systems Empire / CEO LOO | `aion-docs`, `aion-core`, `aion-data`, `aion-runtime`, `aion-infra`, `aion-products` | Partial — GE-001 UNVERIFIED; CRM-001 Gate B PASS | **GROW** | Verified customer outcomes + collected cash with governed execution |
| PR-REV | Revenue OS / Revenue Capture Suite | AION Systems commercial + Runtime | `aion-products`, Runtime CRM adapters, Twenty | Fake RCAOS AT green; live Twenty Gate C/D open | **GROW** | Inquiry → qualified appointment with baseline vs result |
| PR-TOP | TechOps / Security Infrastructure | AION TechOps | `aion-docs` offers + `aion-security` (satellite) | Offer ladder drafted; Secure Execution gates open | **GROW** | Evidence-backed security/AI readiness for SMBs |
| PR-COP | Revenue Copilot | Products | `aion-products` | Auth landed; Gate A 5-call pilot open (AIO-70) | **VALIDATE** | ≥5 real eligible calls with measured assist quality |
| PR-CRE | Creative OS | Creative module owner | `aion-creative-os-CVD` (external) | Local Phase 1 intake; not connected | **VALIDATE** | Authorized creative production with cost evidence |
| PR-WS | Workspace / Command Center | Workspace maintainer | `AION-Command-Center` | Static registry; modules mostly `planned` | **VALIDATE** | Honest planned/connected/available states |
| PR-SEN | Sentinel identity plane | Security + Runtime | `AION-SENTINEL`, Runtime Keycloak gateway | Draft/PR; staging NOT APPROVED (AIO-121) | **VALIDATE** | Verified principal binding; no self-provision |
| PR-FRG | Forge software factory | Engineering | `aion-forge` / related factory repos | Workers DISABLED; staging gated | **INCUBATE** | Governed PR/test workflow without parallel control plane |
| PR-DE | Digital Empire (community/education) | Digital Empire owner | Community surfaces | Strategy VALIDATE | **VALIDATE** | Paid education / community trust without P0 distraction |
| PR-GS | G-Star Empire Records | Label owner | Media/label surfaces | Strategy INCUBATE | **INCUBATE** | Artist/label ops proof on owned IP first |
| PR-SG | Synapse Grid Labs | Labs | Research only | PAUSED | **PAUSE** | No funded delivery |
| PR-PO | Personal Ops | Founder | n/a | Protected capacity | **PROTECTED** | Preserve founder operating capacity |

## Admission rule

New rows require an [Investment Committee](investment-committee.md) decision
record (or Holding equivalent) with: owner, repo, status, value goal, kill
condition, and budget ceiling. Removing or promoting a row updates this file
in the same PR that records the decision.

## Explicit non-claims

- Registry presence ≠ funded build
- Status **VALIDATE** ≠ live customer writes authorized
- Module listed here may remain Workspace `planned` until acceptance evidence
  exists (see Product Module Contract / ADR-015)
