# Offers

Commercial and delivery artifacts that package AION capabilities for clients.
These documents are **sales/delivery contracts**, not Runtime APIs — but they
must stay consistent with governance ADRs and the
[Security Infrastructure Standard v1](../architecture/security-infrastructure-standard-v1.md).

## Operating philosophy

**Outcome-Based Systems Engineering** is the proposed central operating model:
clients buy measurable results; AION designs, deploys, and operates the governed
systems that produce them.

| Document | Purpose |
|---|---|
| [aion-outcome-based-services-blueprint.md](aion-outcome-based-services-blueprint.md) | Single blueprint connecting offers, architecture, FDE training, sales process, and scorecard |
| [ADR-014](../adr/ADR-014-outcome-based-systems-engineering.md) | Decision record (**Accepted** 2026-10-12) |
| [aion-pilot-to-revenue-program.md](aion-pilot-to-revenue-program.md) | **P0 operating priority** — first design-partner pilot → revenue proof (No Shorts proposed) |
| [revenue-capture-appointment-os/](revenue-capture-appointment-os/) | Flagship Sprint pack — process contract, GHL caps, baseline, demo workflow |

Commercial pillars: **Revenue Systems**, **Operations Systems**, **Managed AI
Infrastructure**. Flagship productized process: **Revenue Capture & Appointment OS**.

TechOps (below) remains the security substrate that makes autonomous digital
labor operable — complementary, not replaced.

## TechOps ladder (three layers)

| Layer | Job | Primary artifacts |
|---|---|---|
| **1 — Secure Digital Foundation** | Make the SMB safe enough for software and AI to run parts of the company | [AI-Era Identity Protection](techops-ai-era-identity-protection.md); Credential & Token Inventory (K); Certificate & Trust (N); Emergency Web (W); Ransomware resilience (RR) |
| **2 — Secure AI Execution** | Agent identity, registry, gateway, Observe/Assist/Execute, credential isolation, financial-action policy, supply-chain trust gate, approvals, evidence, kill/revoke | Audit AI/Agents + Supply-chain (M); AIO-44/45; [action-tiers.md](../governance/action-tiers.md); [financial-action-policy.md](../governance/financial-action-policy.md) |
| **3 — Continuous Assurance** | Connector-sourced observe → investigate → recommend → approve → remediate → verify | [Security Operations Lite](techops-security-operations-lite.md) → [continuous-assurance-v1.md](../architecture/continuous-assurance-v1.md) (AIO-47) |

Front door: [Security & Agent Readiness Audit](techops-security-agent-readiness-audit.md)
→ client packaging: [Governance & Security Infrastructure Audit v2](techops-governance-security-infrastructure-audit-v2.md) (AIO-48).

**Positioning:** TechOps is not another cybersecurity reseller. It is the
infrastructure layer that makes autonomous digital labor operable for SMBs.

## Vertical overlays (do not fork architecture)

| Vertical | Offer language | Notes |
|---|---|---|
| Accounting | [Secure Accounting AI Foundation](techops-secure-accounting-ai-foundation.md) + [governance policy template](techops-accounting-ai-governance-policy-template.md) | Strongest near-term security wedge (AIO-50). Discovery lead: client-data protection + governed financial workflows ([financial-action-policy.md](../governance/financial-action-policy.md)) |
| Auto dealers / rentals | [Dealer/Rental security & pricing governance](techops-dealer-rental-security-overlay.md) | Authority bands; financial/contract human-gated (AIO-51). Discovery lead: vendor-access + operational continuity / management-plane |
| Electrical contractors | [Secure Digital Contractor Infrastructure](techops-secure-digital-contractor-infrastructure.md) | OT/IoT = discovery only (AIO-49). Discovery lead: procurement + project-ops readiness before industrial cybersecurity |

## Document index

| Document | Purpose |
|---|---|
| [aion-outcome-based-services-blueprint.md](aion-outcome-based-services-blueprint.md) | Outcome-Based Services Blueprint (commercial + technical operating model) |
| [aion-pilot-to-revenue-program.md](aion-pilot-to-revenue-program.md) | Pilot-to-Revenue Program — 14-day plan, acceptance metrics, GE-001 live gate |
| [revenue-capture-appointment-os/](revenue-capture-appointment-os/) | Revenue Capture & Appointment OS — Sprint 1 locked pack + mission link |
| [techops-security-agent-readiness-audit.md](techops-security-agent-readiness-audit.md) | Front-door audit scorecard + readiness bands |
| [techops-governance-security-infrastructure-audit-v2.md](techops-governance-security-infrastructure-audit-v2.md) | **AIO-48** Audit v2 pack (scorecard add-ons, exec/appendix/roadmap/offer map) |
| [aion-customer-zero-security-baseline-2026-10-06.md](aion-customer-zero-security-baseline-2026-10-06.md) | **AIO-46** AION-as-customer-zero scored baseline |
| [continuous-assurance-monthly-report-template.md](continuous-assurance-monthly-report-template.md) | **AIO-47** monthly client-facing assurance report |
| [techops-ai-era-identity-protection.md](techops-ai-era-identity-protection.md) | Foundation SKU — Human + Machine Identity hardening |
| [techops-secure-accounting-ai-foundation.md](techops-secure-accounting-ai-foundation.md) | **AIO-50** Accounting vertical packaging + audit module |
| [techops-accounting-ai-governance-policy-template.md](techops-accounting-ai-governance-policy-template.md) | **AIO-50** Client AI/automation governance policy template |
| [techops-secure-digital-contractor-infrastructure.md](techops-secure-digital-contractor-infrastructure.md) | **AIO-49** Electrical/contractor overlay |
| [techops-dealer-rental-security-overlay.md](techops-dealer-rental-security-overlay.md) | **AIO-51** Dealer/rental overlay + pricing governance |
| [techops-security-operations-lite.md](techops-security-operations-lite.md) | Recurring assurance / SecOps Lite SKU (design) |
| [aion-data-sovereignty-licensing-execution-spec.md](aion-data-sovereignty-licensing-execution-spec.md) | **DSL-001** 90-day Data Sovereignty & Licensing validation (P-AIO-9) |

## Program tracker

**Outcome / pilot:** [AIO-71](https://linear.app/aion-empire/issue/AIO-71/p0-pilot-to-revenue-no-shorts-electrical-design-partner-rcaos) (Pilot-to-Revenue; blocked by AIO-35 / AIO-37).  
**TechOps:** AIO-43 … AIO-52. Build order: **AIO-43 → AIO-44 → AIO-45 → AIO-46 → AIO-47 (Continuous Assurance) → AIO-48 (Audit v2) → AIO-49/50/51 (overlays) → AIO-52 (offer ladder)**.  
**Data Sovereignty commercial validation:** [P-AIO-9](https://linear.app/aion-empire/project/aion-data-sovereignty-and-licensing-validation-sprint-a23e98149964) · [execution spec](aion-data-sovereignty-licensing-execution-spec.md) (does not replace Runtime GE-001 or SOV Phase 2).
