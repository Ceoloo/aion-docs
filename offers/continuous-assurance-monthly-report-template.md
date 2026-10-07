# Continuous Assurance — Monthly Client Report Template

- **SKU:** Continuous Assurance / Security Operations Lite
- **Spec:** [../architecture/continuous-assurance-v1.md](../architecture/continuous-assurance-v1.md)
- **Issue:** AIO-47

Copy per tenant per month. Prefer evidence URIs over screenshots.

---

## Cover

| Field | Value |
|---|---|
| Client / tenant | |
| Period | YYYY-MM-01 → YYYY-MM-DD |
| Report version | CA-v1 |
| Prepared by | |
| SIS version | security-infrastructure-standard-v1 |
| Prior automation readiness band | |
| Current automation readiness band | |

## 1. Executive summary

- One paragraph: what improved, what regressed, whether Execute-tier remains
  blocked by hard-stops (Identity &lt; 8, Credentials &lt; 6, I3 = 0).
- Headline counts: open critical __ / high __ / medium __ ; closed this month __.

## 2. Open critical & high findings

| Finding ID | Check ID | Severity | Age (days) | Owner | Next action | Evidence URI |
|---|---|---|---|---|---|---|
| | | | | | | |

## 3. Check coverage

| Check ID | Domain | Last run | Status | Notes |
|---|---|---|---|---|
| CA-EXP-01 | Exposure | | pass/fail/unknown | |
| CA-ID-01 | Identity | | | |
| CA-CT-01 | Credentials | | | |
| CA-CL-01 | Cloud/SaaS | | | |
| CA-BK-01 | Backup | | | |
| CA-AG-01 | Agents | | | |
| CA-AG-02 | Agents | | | |
| CA-RT-01 | Runtime | | | |
| CA-VN-01 | Vulns | | | |
| CA-RV-01 | Revoke readiness | | | |
| … | | | | |

(Full catalog: continuous-assurance-v1.md §2.)

## 4. Drift highlights

### Identity
-

### Credentials & tokens
-

### Agent registry / permission drift
- Link: `GET /v1/registry/review` export for period

### Backup / restore evidence
-

## 5. Remediations completed

| Date | Playbook | Approved by | Verification check | Result |
|---|---|---|---|---|
| | | | | |

## 6. Human-gated decisions

| Count | R3 gates | Low-confidence holds | Samples (evidence IDs) |
|---|---|---|---|
| | | | |

## 7. Recommendations (next 30 days)

Map to TechOps layers:

1. Secure Digital Foundation —
2. Secure AI Execution —
3. Continuous Assurance —

## 8. Appendix — evidence index

| evidenceId | checkId | observedAt | URI |
|---|---|---|---|
| | | | |
