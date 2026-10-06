# AION Customer-Zero Security Baseline Assessment

- **Issue:** [AIO-46](https://linear.app/aion-empire/issue/AIO-46)
- **Date:** 2026-10-06
- **Standard:** Security Infrastructure Standard v1
- **Scorecard:** [TechOps Security & Agent Readiness Audit](../../offers/techops-security-agent-readiness-audit.md)
- **Inventory:** [aion-agent-identity-inventory-2026-10-06.md](aion-agent-identity-inventory-2026-10-06.md)

AION is customer zero. Scores use the audit 0–3 scale. Unknown = **0**.

## Domain scores

| Domain | Score | Max | Notes |
|---|---|---|---|
| A. Identity (human) | 6 | 15 | MFA/ops practices exist but not fully evidenced as scored controls |
| B. Devices | 4 | 12 | Endpoint posture not fully evidenced for all operator devices |
| C. Network | 4 | 12 | Production network controls owned by infra; audit evidence incomplete |
| D. Cloud | 6 | 12 | Tenant hardening partial; log retention needs evidence pack |
| E. Data | 6 | 12 | Classification + RLS scaffolds; regulated-data map incomplete |
| F. Backup / recovery | 4 | 12 | Related AIO-7; restore-test evidence still required for Met |
| G. SaaS | 5 | 12 | Inventory tribal; shadow-AI policy not fully operationalized |
| H. Automation | 6 | 12 | Integrations improving; secret hygiene rules strong in repos |
| I. AI / Agents | 8 | 15 | Registry + tiers + Trust Score specs landed; production inventory incomplete |
| J. Observability | 7 | 12 | Execution/audit spine exists; centralized alert review cadence weak |
| K. Credentials & tokens | 5 | 15 | No committed secrets; full human/service/agent token inventory still Gap |
| **Total A–K** | **61** | **141** | |
| L. Connected-infra discovery | 1 | 9 | Informational only |

### Automation readiness

```
Identity(6) + Credentials(5) + Automation(6) + AI/Agents(8) + Observability(7) = 32 / 69
```

| Band | Result |
|---|---|
| **Assist-ready** | Yes (32 is inside 23–45) |
| Execute-ready (gated) | **No** — below 46; Credentials & Identity hard-stop risk |

**Hard-stop flags:** Identity 6 &lt; 8 → not Execute-ready. Credentials 5 &lt; 6 → not Execute-ready.

## Top 10 risks / gaps

1. Human MFA / session-response evidence pack incomplete for AION ops accounts
2. Credential & Token Inventory (human/service/agent) not yet a living owned artifact
3. Product agents still run outside Runtime registry (Revenue Copilot path)
4. Long-lived LoB credentials may still exist in some environments (progressive isolation)
5. Backup restore test ≤ 90 days not evidenced (AIO-7)
6. Agent registry completeness not enforced with `requireComplete` in production
7. Kill/contain tabletop for agent identities not yet run
8. Continuous Assurance connectors not built (AIO-47)
9. Shadow-AI / acceptable-use operationalization incomplete
10. Centralized alert review ownership/cadence unclear

## 30 / 60 / 90 remediation

### 30 days

- Enforce `POST /v1/actors` with `requireComplete: true` for production tenants
- Register Revenue Copilot / workforce agents into Runtime registry
- Publish AION Credential & Token Inventory (ownership class filled)
- Complete MFA evidence for admin/finance-equivalent AION roles
- Run revoke/contain tabletop (`POST /v1/actors/:id/revoke`)

### 60 days

- Finish Secure Execution Layer credential isolation (AIO-45 implementation)
- Evidenced backup restore test (AIO-7)
- Close Identity score ≥ 8 and Credentials ≥ 6 hard stops
- Wire executionEvidence links from registry → executions queries

### 90 days

- Continuous Assurance check catalog live (AIO-47)
- Re-score customer-zero to Execute-ready (gated) band
- Convert remediated controls into client case-study proof pack

## Evidence already verified

| Control area | Evidence |
|---|---|
| No secrets in git | Hard rule + repo hygiene |
| Agent URI / Actor contracts | `aion-core` agent-identity + actor |
| Agent Identity Registry | Core/Data/Runtime AIO-44 PRs |
| Observe / Assist / Execute | ADR-007 + action-tiers |
| Execution Gateway | ADR-003 + Runtime routes |
| Trust Score | ADR-006 + Runtime attach path |
| SIS v1 standard | `architecture/security-infrastructure-standard-v1.md` |

## Case-study candidates (after remediation)

- Registry + revoke/contain story for SMB Secure AI Execution
- Customer-zero scored audit method for TechOps front door
- Gateway boundary vs API-key automation (dealer/accounting packaging)

## Invariants

- This assessment does not claim Execute-ready autonomy for AION itself.
- Re-score after 30-day actions before raising the automation band.
