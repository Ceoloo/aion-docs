# Offers

Commercial and delivery artifacts that package AION capabilities for clients.
These documents are **sales/delivery contracts**, not Runtime APIs — but they
must stay consistent with governance ADRs and the
[Security Infrastructure Standard v1](../architecture/security-infrastructure-standard-v1.md).

## TechOps ladder (three layers)

| Layer | Job | Primary artifacts |
|---|---|---|
| **1 — Secure Digital Foundation** | Make the SMB safe enough for software and AI to run parts of the company | [AI-Era Identity Protection](techops-ai-era-identity-protection.md); Credential & Token Inventory (audit domain K) |
| **2 — Secure AI Execution** | Agent identity, registry, gateway, Observe/Assist/Execute, credential isolation, approvals, evidence, kill/revoke | Audit AI/Agents domain; AIO-44/45; [action-tiers.md](../governance/action-tiers.md) |
| **3 — Continuous Assurance** | Connector-sourced observe → investigate → recommend → approve → remediate → verify | [Security Operations Lite](techops-security-operations-lite.md) → AIO-47 |

Front door: [Security & Agent Readiness Audit](techops-security-agent-readiness-audit.md).

**Positioning:** TechOps is not another cybersecurity reseller. It is the
infrastructure layer that makes autonomous digital labor operable for SMBs.

## Vertical overlays (do not fork architecture)

| Vertical | Offer language | Notes |
|---|---|---|
| Accounting | [Secure Accounting AI Foundation](techops-secure-accounting-ai-foundation.md) | Strongest near-term security wedge |
| Auto dealers / rentals | Authority bands on the audit + action tiers | Financial/contract always human-gated |
| Electrical contractors | Secure Digital Contractor Infrastructure | OT/IoT = discovery category only |

## Document index

| Document | Purpose |
|---|---|
| [techops-security-agent-readiness-audit.md](techops-security-agent-readiness-audit.md) | Front-door audit scorecard + readiness bands |
| [techops-ai-era-identity-protection.md](techops-ai-era-identity-protection.md) | Foundation SKU — Human + Machine Identity hardening |
| [techops-secure-accounting-ai-foundation.md](techops-secure-accounting-ai-foundation.md) | Accounting vertical packaging |
| [techops-security-operations-lite.md](techops-security-operations-lite.md) | Recurring assurance / SecOps Lite SKU (design) |

## Program tracker

Linear: AIO-43 … AIO-52 (TechOps Security Infrastructure Layer).  
This-week build order: **AIO-43 → AIO-44 → AIO-45 → AIO-46**.
