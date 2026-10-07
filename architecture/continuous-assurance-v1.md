# Continuous Assurance v1

- **Status:** Active (AIO-47)
- **Date:** 2026-10-06
- **Standard map:** SIS-CA-01…05 in
  [security-infrastructure-standard-v1.md](security-infrastructure-standard-v1.md) §3.12
- **Commercial SKU:** [../offers/techops-security-operations-lite.md](../offers/techops-security-operations-lite.md)
- **Depends on:** SIS v1 (AIO-43), Agent Identity Registry (AIO-44),
  Secure Execution Layer (AIO-45), customer-zero baseline method (AIO-46)

Continuous Assurance verifies the security baseline **remains true** after
implementation. It is designed around **connectors and evidence**, not humans
manually reviewing dashboards.

```text
Observe → investigate → recommend → human approve → remediate → verify
```

This document is the **prototype control-loop contract**: check catalog,
cadence/severity, evidence format, escalation path, and monthly report.

## 1. Operating principles

1. Prefer connector-sourced evidence over screenshots (SIS-CA-02).
2. Drift domains must cover exposure, identity, credentials, cloud/SaaS,
   backup, agent permissions/behavior, runtime health, vulns, and revoke
   readiness (SIS-CA-03).
3. High-impact remediation is always human-gated (SIS-CA-04; R3 always).
4. Every open finding has a named owner and next action (SIS-IR-01).
5. Unknown / unverified checks score **0** on the audit (fail closed).

## 2. Check catalog (SIS-CA-01)

Stable check IDs. Each check produces an `AssuranceEvidence` record (§4).

| Check ID | Domain | Title | Default cadence | Severity if fail |
|---|---|---|---|---|
| `CA-EXP-01` | External exposure | Unexpected public asset / open admin surface | Daily | high |
| `CA-EXP-02` | External exposure | DNS / TLS cert expiry ≤ 14 days | Daily | medium |
| `CA-ID-01` | Identity posture | Privileged accounts without phishing-resistant MFA | Daily | critical |
| `CA-ID-02` | Identity posture | Stale / risky sessions vs revoke playbook | Daily | high |
| `CA-ID-03` | Identity posture | New OAuth / app-consent grants outside allow-list | Hourly→Daily | high |
| `CA-CT-01` | Credentials | Credential & Token Inventory vs live grants drift | Daily | high |
| `CA-CT-02` | Credentials | Long-lived LoB keys detected in agent/runtime scopes | Daily | critical |
| `CA-CT-03` | Credentials | Secrets in git / shared docs (negative scan) | Daily | critical |
| `CA-CL-01` | Cloud / SaaS drift | Tenant hardening baseline drift | Weekly | high |
| `CA-CL-02` | Cloud / SaaS drift | External sharing / admin role standing privilege drift | Weekly | medium |
| `CA-CL-03` | Cloud / SaaS drift | Audit log retention below 180 days | Weekly | medium |
| `CA-BK-01` | Backup | Backup freshness beyond RPO | Daily | high |
| `CA-BK-02` | Backup | Restore evidence older than 90 days | Weekly | high |
| `CA-AG-01` | Agent permission drift | Registry incomplete for Execute-tier agents | Continuous | critical |
| `CA-AG-02` | Agent permission drift | Observed agent not in registry (SIS-AG-10) | Continuous | critical |
| `CA-AG-03` | Agent permission drift | Tools / data_scope / policy_version drift vs registry | Daily | high |
| `CA-AG-04` | Agent behavior | Trust Score / deny spikes for tenant | Continuous | medium |
| `CA-RT-01` | Runtime / endpoint | Gateway / Runtime health + FeatureGate kill path | Continuous | high |
| `CA-RT-02` | Runtime / endpoint | Endpoint protection coverage gap | Daily | high |
| `CA-VN-01` | Vulnerabilities | Critical CVE on internet-facing or agent host | Daily | critical |
| `CA-VN-02` | Vulnerabilities | High CVE unpatched past SLA | Weekly | high |
| `CA-RV-01` | Revoke readiness | Agent revoke/contain tabletop stale (>90d) | Monthly | high |
| `CA-RV-02` | Revoke readiness | Suspended/revoked agent still executing | Continuous | critical |

Connector sources (examples): IdP export, endpoint console API, mail security,
SaaS admin API, vault/inventory CSV, Runtime
`GET /v1/registry/review` + `/v1/registry/inventory`, executions ledger,
backup vendor API, vuln scanner.

## 3. Cadence and severity model

### Cadence

| Cadence | Meaning |
|---|---|
| **Continuous** | Event-driven or ≤ 15 min poll where connectors allow |
| **Hourly→Daily** | Near-real-time when connector supports; else daily batch |
| **Daily** | Once per day per tenant |
| **Weekly** | Once per week; included in weekly digest |
| **Monthly** | Tabletop / report-grade checks |

### Severity

| Severity | Client impact | Escalation |
|---|---|---|
| **critical** | Active compromise path or Execute-tier ungovered agent | Immediate human path (§5); acknowledge ≤ 1 business hour |
| **high** | Material baseline break | Acknowledge ≤ 4 business hours |
| **medium** | Drift with limited blast radius | Next business-day digest |
| **low** / **info** | Hygiene / informational | Monthly report |

Severity maps to AION risk vocabulary when remediation is automated:
critical/high → treat as R2–R3 for gating; never auto-remediate R3.

### Confidence → action (SKU alignment)

| Confidence | Risk | Action |
|---|---|---|
| High | Low (R0–R1) | Auto-execute allow-listed remediation |
| High | Moderate (R2) | Policy-dependent; default human gate |
| Any | High (R3) / critical severity | **Always** human gate |
| Low | Any | Human triage; no auto-remediation |

## 4. Evidence storage format

Every check run emits one evidence object (store as JSON in the assurance
ledger / object store; link from monthly report). Prefer URIs to connector
exports over embedded blobs.

```json
{
  "evidenceId": "cae_<uuid>",
  "checkId": "CA-AG-02",
  "tenantId": "acme-dealership",
  "observedAt": "2026-10-06T12:00:00.000Z",
  "cadence": "continuous",
  "status": "fail",
  "severity": "critical",
  "summary": "Observed agent URI not in Agent Identity Registry",
  "connector": {
    "system": "aion-runtime",
    "query": "GET /v1/registry/review?observed=...",
    "evidenceUri": "https://…/assurance/cae_<uuid>/raw.json"
  },
  "sisControls": ["SIS-CA-03", "SIS-AG-10"],
  "recommendedAction": {
    "playbookId": "pb_register_or_revoke_agent",
    "requiresApproval": true,
    "riskLevel": "R3"
  },
  "owner": "security@acme.example",
  "verification": {
    "status": "pending",
    "verifiedAt": null,
    "method": "re-run check CA-AG-02"
  }
}
```

| Field | Required | Notes |
|---|---|---|
| `evidenceId` | M | Stable id (`cae_…`) |
| `checkId` | M | From §2 catalog |
| `tenantId` | M | Never ambient |
| `observedAt` | M | ISO-8601 |
| `status` | M | `pass` \| `fail` \| `unknown` \| `error` |
| `severity` | M | From §3 |
| `connector` | M | System + query/export pointer |
| `sisControls` | M | SIS IDs attested |
| `recommendedAction` | R | Playbook + gate flag |
| `owner` | M when fail | Named human |
| `verification` | M after remediate | Re-check proof |

**Unknown = fail for scoring.** `status: unknown` counts as audit 0 until a
connector produces pass/fail.

## 5. Human escalation path

```text
connector signal
  → check evaluation (status + severity + confidence)
  → if fail:
       create AssuranceFinding
       assign owner
       if severity ∈ {critical, high} OR risk ≥ R3 OR confidence low:
         notify on-call / tenant security owner
         require human approval before remediation playbook
       else if allow-listed auto-remediation:
         execute → write incident → verify (re-run check)
  → monthly report includes open / closed / MTTR
```

| Step | Owner | SLA (default) |
|---|---|---|
| Acknowledge critical | Tenant security owner + AION assurance operator | ≤ 1 business hour |
| Acknowledge high | Named finding owner | ≤ 4 business hours |
| Approve R3 remediation | Human with approve role (Runtime identity plane) | Before playbook runs |
| Verify | Same check ID re-run | Within 1 cadence window after remediate |

Escalation contacts live in the tenant assurance profile (not in agent
metadata). Agent contain uses Registry revoke/suspend (AIO-44) + FeatureGate
(AIO-45) as allow-listed playbooks.

## 6. Monthly client-facing report format (SIS-CA-05)

Template: [../offers/continuous-assurance-monthly-report-template.md](../offers/continuous-assurance-monthly-report-template.md)

Minimum sections:

1. Executive summary (band movement vs prior month)
2. Open critical/high findings (owner, age, next action)
3. Check coverage table (catalog ID → last run → status)
4. Drift highlights (identity, credentials, agents, backup)
5. Remediations completed + verification proof links
6. Human-gated decisions (count + samples)
7. Recommendations → Foundation / Secure AI Execution / Assurance upsell
8. Appendix: evidence index (`evidenceId` → URI)

## 7. Prototype scope (v1)

**In**

- Catalog + cadence + evidence schema (this doc)
- Runtime registry connectors (`CA-AG-*`, `CA-RV-02`, partial `CA-RT-01`)
- Monthly report template
- Mapping onto Security Ops Lite SKU loop

**Out / follow-on**

- Full multi-tenant assurance service implementation
- Every IdP/endpoint/vuln connector productionized
- 24×7 SOC staffing
- OT/ICS monitoring

## 8. Acceptance (AIO-47)

| Criterion | Evidence |
|---|---|
| Check catalog | §2 |
| Cadence and severity model | §3 |
| Evidence storage format | §4 |
| Human escalation path | §5 |
| Monthly client-facing report format | §6 + offers template |

## Related

- [agent-identity-registry.md](agent-identity-registry.md)
- [secure-execution-layer-v1.md](secure-execution-layer-v1.md)
- [../offers/aion-customer-zero-security-baseline-2026-10-06.md](../offers/aion-customer-zero-security-baseline-2026-10-06.md)
