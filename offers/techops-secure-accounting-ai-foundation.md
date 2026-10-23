# Secure Accounting AI Foundation

Accounting vertical overlay for TechOps (TechOps Monday Brief 2026-10-05;
Linear **AIO-50**).

**The opportunity is not merely “AI automation for accountants.”**  
It is **secure AI infrastructure for financial workflows**.

An accounting agent touching client documents, email, accounting SaaS, or
payment information must have: explicit identity · narrow authority · human
approval · audit trail.

**Deliverables for AIO-50:** this offer + [audit module](#audit-module-overlay-scores)
+ [governance policy template](techops-accounting-ai-governance-policy-template.md).
Engagements use [Audit v2](techops-governance-security-infrastructure-audit-v2.md)
as the front door.

## SKU one-liner

> Establish identity/MFA, secure document flow, AI acceptable-use, machine/agent
> identity inventory, financial-action approval gates, logging, and recovery —
> **before** deploying meaningful automation.

## Audit module (overlay scores)

Score 0–3. Add to Audit v2 after A–K + M. Intensifies FTC Safeguards-style
program hygiene for firms handling client financial data (not a legal opinion).

| # | Control | Emphasize | SIS / audit map |
|---|---|---|---|
| AC1 | MFA/passkeys on all staff touching client tax docs | SIS-HI-01/02 | A |
| AC2 | Secure document intake + classified storage | SIS-DA-* | E |
| AC3 | Written AI acceptable-use + quarterly shadow-AI discovery | SIS-SA-03 | G3 |
| AC4 | Credential & Token Inventory complete (human/service/agent) | SIS-CT-* | K |
| AC5 | Agent registry fields complete for every production agent | SIS-AG-01/02 | I1 |
| AC6 | Financial-action human gates (wires, filings, payments, vendor bank change) | SIS-AG-04 | I3 |
| AC7 | Dual control on payment initiation / vendor bank changes | Overlay | I3 |
| AC8 | Immutable evidence path for agent + approval decisions | SIS-OB-*, SIS-GX-08 | J/I5 |
| AC9 | Backup + restore proven for identity, files, accounting SaaS | SIS-BK-* | F |
| AC10 | Incident response includes agent/token revoke within defined SLA | SIS-AG / CT | J2 |

**Accounting overlay subtotal:** __ / 30

### Hard stops (accounting)

- AC1 &lt; 2 or AC6 = 0 → cannot be Execute-ready.
- AC3 = 0 → no Assist agents on client documents.
- AC7 = 0 → no payment automation of any kind.

## Delivery sequence

| Step | Control cluster | SIS / audit map |
|---|---|---|
| 1 | Identity / MFA hardening (all staff touching client tax docs) | SIS-HI-*; audit A |
| 2 | Secure document intake + data boundaries | SIS-DA-*; audit E |
| 3 | AI acceptable-use + shadow-AI discovery | SIS-SA-03; audit G3 |
| 4 | Credential & Token Inventory (human / service / agent) | SIS-CT-*; audit K |
| 5 | Machine / agent identity inventory (mandatory registry fields) | SIS-AG-*; audit I |
| 6 | Financial-action approval gates (wires, filings, payments) | SIS-AG-04 / SIS-GX-06; I3 |
| 7 | Logging + immutable evidence path | SIS-OB-*; SIS-GX-08 |
| 8 | Backup / recovery proven | SIS-BK-*; audit F |

Only after Foundation + Secure AI Execution gates are green should Execute-tier
accounting agents be considered (audit hard stops apply).

## Sales emphasis

| Theme | Client language |
|---|---|
| Identity hardening | Passkeys/MFA; stop shared mailboxes as standing identity |
| Document / data boundaries | Client docs stay in classified locations with least privilege |
| Financial-action approvals | No unattended wires, refunds, or tax filing |
| Agent governance | Named owner, purpose, tools, data scope, revoke path |
| Immutable evidence | Prove what the agent did — and that a human approved money moves |

## Dependencies / non-goals

- **Depends on:** [Audit v2](techops-governance-security-infrastructure-audit-v2.md)
  + scorecard [techops-security-agent-readiness-audit.md](techops-security-agent-readiness-audit.md)
  against [Security Infrastructure Standard v1](../architecture/security-infrastructure-standard-v1.md).
- **Pairs with:** [AI-Era Identity Protection](techops-ai-era-identity-protection.md)
  as the human-identity core of Foundation; policy handoff via
  [techops-accounting-ai-governance-policy-template.md](techops-accounting-ai-governance-policy-template.md).
- **Does not include:** unattended tax filing, payment initiation without R3
  human gate, or a full SOC build.
- **Recurring uplift:** Continuous Assurance / Security Ops Lite is justified —
  you are protecting regulated client-information workflows.

## Acceptance (AIO-50)

| Criterion | Evidence |
|---|---|
| Audit module | AC1–AC10 table |
| Secure Accounting AI Foundation offer | This document (SKU + delivery sequence) |
| Governance policy template | [techops-accounting-ai-governance-policy-template.md](techops-accounting-ai-governance-policy-template.md) |
