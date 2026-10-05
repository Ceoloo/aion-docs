# Secure Accounting AI Foundation

Accounting vertical overlay for TechOps (TechOps Monday Brief 2026-10-05;
Linear AIO-50 packaging precursor).

**The opportunity is not merely “AI automation for accountants.”**  
It is **secure AI infrastructure for financial workflows**.

An accounting agent touching client documents, email, accounting SaaS, or
payment information must have: explicit identity · narrow authority · human
approval · audit trail.

## SKU one-liner

> Establish identity/MFA, secure document flow, AI acceptable-use, machine/agent
> identity inventory, financial-action approval gates, logging, and recovery —
> **before** deploying meaningful automation.

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

- **Depends on:** [Security & Agent Readiness Audit](techops-security-agent-readiness-audit.md)
  scored against [Security Infrastructure Standard v1](../architecture/security-infrastructure-standard-v1.md).
- **Pairs with:** [AI-Era Identity Protection](techops-ai-era-identity-protection.md)
  as the human-identity core of Foundation.
- **Does not include:** unattended tax filing, payment initiation without R3
  human gate, or a full SOC build.
- **Recurring uplift:** Continuous Assurance / Security Ops Lite is justified —
  you are protecting regulated client-information workflows.
