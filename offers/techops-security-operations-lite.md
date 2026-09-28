# TechOps Security Operations Lite

Recurring managed-service SKU (Linear AIO-22; TechOps Monday Brief 2026-09-14).

Sits between traditional MSP “we watch alerts” and an enterprise SOC.
**Design only for v1** — do not build a full SOC platform in this SKU definition.

## SKU one-liner

> We operate a governed detect → triage → (approved) remediate loop for your
> identity, endpoint, mail, and SaaS signals — with humans on low-confidence
> or high-impact actions.

## Target operating loop

```
telemetry → detection → AI triage → confidence scoring
        → approved remediation → incident record → owner notification
```

| Confidence | Risk | Action |
|---|---|---|
| High | Low (R0–R1) | Auto-execute allow-listed remediation |
| High | Moderate (R2) | Policy-dependent; default human gate |
| Any | High (R3) | **Always** human gate |
| Low | Any | Human triage; no auto-remediation |

Reuse AION [human gates](../governance/human-gates.md),
[risk levels](../governance/risk-levels.md), and
[Agent Trust Score](../governance/agent-trust-score.md) semantics for
automation that AION itself runs.

## Scope (in)

- Identity / sign-in anomaly review (tied to Foundation Identity Protection)
- Endpoint medium/high alerts with known playbooks
- Mail phishing / AiTM indicators
- SaaS OAuth grant anomalies
- Weekly digest + monthly risk review
- Incident record with owner + next action

## Scope (out / exclusions)

- 24×7 dedicated SOC analysts as a staffing model (unless separately contracted)
- Custom detection engineering for exotic OT/ICS
- Penetration testing / red team (separate engagement)
- Building or hosting a full SIEM product in v1
- Unattended financial, destructive, or production-release remediation

## Dependencies

1. **TechOps Foundation** — especially
   [AI-Era Identity Protection](techops-ai-era-identity-protection.md)
2. **Secure Automation Layer** — agent identities, permissions, Action Tiers,
   human gates for any AION-driven remediation
3. Telemetry sources actually connected (IdP, endpoint, mail)

If Foundation Identity score is below audit hard-stop, sell Foundation first —
Ops Lite without identity hardening is theater.

## SLA posture (v1 draft)

| Class | Target |
|---|---|
| Critical identity compromise indicators | Acknowledge ≤ 1 business hour |
| High endpoint / mail | Acknowledge ≤ 4 business hours |
| Medium / informational | Next business-day digest |
| Customer communication | Named owner on every open incident |

Exact contractual SLAs are commercial — this table is the operating default.

## Pricing posture

- Recurring monthly (not one-time “tool dump”)
- Scales with seat count / signal sources, not vanity AI features
- Optional uplift for after-hours coverage
- Accounting vertical: premium relative to general SMB (regulated data)

## Internal operating outline

1. Ingest signals from Foundation-connected sources
2. Normalize to incident candidate (tenant, asset, user, severity)
3. AI triage proposes classification + confidence + recommended action
4. Policy: auto vs gate (R3 always gate; low confidence always human)
5. Remediation only from allow-listed playbooks (reset session, disable token,
   isolate device, quarantine mail)
6. Write incident record + notify owner
7. Feed outcomes into monthly review (false positive rate, time-to-contain)

## Sales one-pager bullets

- Not another dashboard you ignore
- Machine-speed triage with human authority on real risk
- Built on identity hardening + governed automation — not alert spam
- Clear exclusions so buyers know what they are (and are not) buying

## Non-goals

- Replacing the customer’s cyber-insurance obligations
- Guaranteeing zero breaches
- Unrestricted autonomous remediation across the estate
