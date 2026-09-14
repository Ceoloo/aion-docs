# TechOps Security & Agent Readiness Audit

Sales front door for TechOps (Linear AIO-18; TechOps Monday Brief 2026-09-14).

This audit **does not install tools**. It scores readiness to operate autonomous
digital labor safely, then maps findings to:

1. **TechOps Foundation** — identity, devices, cloud, network, backup  
2. **Secure Automation Layer** — integrations, workflow engine, agent identities,
   permissions, human gates  
3. **Managed Intelligence** — agents, evaluation / Trust Score, monitoring,
   cost/ROI, optimization  

Architecture reference: [action-tiers.md](../governance/action-tiers.md),
[agent-trust-score.md](../governance/agent-trust-score.md),
[permissions.md](../governance/permissions.md).

## Scoring scale (each category)

| Score | Meaning |
|---|---|
| **0** | Missing / unknown / unsafe default |
| **1** | Partial / tribal / tool present but ungovered |
| **2** | Documented and mostly enforced |
| **3** | Measured, owned, and fail-closed |

**Category score** = integer 0–3.  
**Domain totals** below. **Automation readiness** derived from AI/Agents +
Automation + Identity + Observability (see formula).

## Scorecard

### A. Identity (weight: critical)

| # | Control | 0 | 1 | 2 | 3 | Notes / evidence |
|---|---|---|---|---|---|---|
| A1 | Phishing-resistant MFA / passkeys on admin + finance | | | | | |
| A2 | Standard users MFA enforced (not optional) | | | | | |
| A3 | OAuth / app-consent restricted; admin approval required | | | | | |
| A4 | Privileged accounts separated; no shared mailboxes as identity | | | | | |
| A5 | Suspicious-session response playbook (AiTM / token theft) | | | | | |

**Identity subtotal:** __ / 15

### B. Devices

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| B1 | Endpoint protection on all workstations / servers | | |
| B2 | Device compliance / MDM for company data access | | |
| B3 | Disk encryption + screen lock policy | | |
| B4 | Local admin rights removed for standard users | | |

**Devices subtotal:** __ / 12

### C. Network

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| C1 | Segmented guest / IoT vs corporate | | |
| C2 | VPN or ZTNA for remote admin access | | |
| C3 | DNS / web filtering for malware & phishing | | |
| C4 | Firewall change ownership + logging | | |

**Network subtotal:** __ / 12

### D. Cloud (M365 / Google / hyperscaler)

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| D1 | Tenant hardening baseline applied | | |
| D2 | External sharing defaults least privilege | | |
| D3 | Admin roles JIT / limited standing privilege | | |
| D4 | Audit logs retained ≥ 180 days | | |

**Cloud subtotal:** __ / 12

### E. Data

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| E1 | Data classes identified (public / internal / confidential / regulated) | | |
| E2 | Access follows least privilege (not “everyone in SharePoint”) | | |
| E3 | Customer/PII / tax / financing data locations mapped | | |
| E4 | DLP or equivalent on mail + files for sensitive classes | | |

**Data subtotal:** __ / 12

### F. Backup / recovery

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| F1 | Backups for identity + cloud files + line-of-business | | |
| F2 | Immutable / offline copy against ransomware | | |
| F3 | Restore tested in last 90 days | | |
| F4 | RPO/RTO documented and owned | | |

**Backup subtotal:** __ / 12

### G. SaaS

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| G1 | Inventory of SaaS with business data | | |
| G2 | Offboarding revokes SaaS within 24h | | |
| G3 | No shadow AI tools with customer data (policy + discovery) | | |
| G4 | Vendor access / integrations reviewed quarterly | | |

**SaaS subtotal:** __ / 12

### H. Automation

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| H1 | Integrations inventory (Zapier/Make/native) with owners | | |
| H2 | Secrets not in shared inboxes / spreadsheets | | |
| H3 | Change control for production automations | | |
| H4 | Failure alerts + human owner for broken flows | | |

**Automation subtotal:** __ / 12

### I. AI / Agents (AION readiness)

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| I1 | Agent identities distinct from shared user logins | | |
| I2 | Observe / Assist / Execute tiers declared per workflow | | |
| I3 | Human gates on financial / destructive / regulated actions | | |
| I4 | Permissions + data-class allow-lists enforced (not prompt-only) | | |
| I5 | Execution Trust Score / audit trail available to operators | | |

**AI/Agents subtotal:** __ / 15

### J. Observability

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| J1 | Centralized auth / mail / endpoint alerts reviewed | | |
| J2 | Incident record + owner notification path | | |
| J3 | Automation/agent runs attributable (who/what/when/tenant) | | |
| J4 | Cost / usage visibility for AI and integrations | | |

**Observability subtotal:** __ / 12

## Totals

| Domain | Max | Score |
|---|---|---|
| Identity | 15 | |
| Devices | 12 | |
| Network | 12 | |
| Cloud | 12 | |
| Data | 12 | |
| Backup | 12 | |
| SaaS | 12 | |
| Automation | 12 | |
| AI / Agents | 15 | |
| Observability | 12 | |
| **Total** | **126** | |

### Automation readiness (derived)

```
readiness = Identity + Automation + AI/Agents + Observability
max = 15 + 12 + 15 + 12 = 54
```

| Band | Range | Meaning |
|---|---|---|
| **Not ready** | 0–17 | Do not deploy Execute-tier agents; Foundation first |
| **Assist-ready** | 18–35 | Observe/Assist OK with supervision; no unattended Execute |
| **Execute-ready (gated)** | 36–45 | Execute allow-lists + R3 gates; monitor Trust Scores |
| **Managed autonomy candidate** | 46–54 | Candidate for earned autonomy + Security Ops Lite |

**Hard stop:** Identity &lt; 8 **or** AI/Agents I3 = 0 → cannot be Execute-ready
regardless of total.

## Deliverable template (client output)

1. **Current risk** — top 5 findings by blast radius (prefer Identity, Data,
   Backup, AI Execute paths).
2. **Automation readiness** — band + hard-stop flags.
3. **Recommended architecture** — map gaps → Foundation / Secure Automation /
   Managed Intelligence.
4. **Implementation roadmap** — 30 / 60 / 90 day ordered work.
5. **Monthly managed-services requirement** — what AION operates vs client owns.

### Vertical packaging notes

| Vertical | Emphasize | Offer language |
|---|---|---|
| Electrical | Job docs, estimates, field devices, AP/AR | Contractor Cloud Foundation → cash collection workflows |
| Accounting | FTC Safeguards, MFA, document intake, tax data | Secure Accounting Infrastructure |
| Dealer / rental | PII, financing, DMS writes | Observe → Assist → Execute; money always gated |

## SKU mapping cheat sheet

| Finding cluster | SKU |
|---|---|
| MFA, consent, sessions, devices | Foundation — [AI-Era Identity Protection](techops-ai-era-identity-protection.md) |
| Backup, cloud hardening, network | Foundation |
| Integration chaos, secrets, agent identity | Secure Automation Layer |
| No Trust Score / no gates / Execute sprawl | Secure Automation + Managed Intelligence |
| Alert fatigue / no response | [Security Operations Lite](techops-security-operations-lite.md) |

## Invariants

- Audit evidence beats screenshots of tool logos.
- Unknown scores **0**, not “assumed fine.”
- Execute-tier recommendations require Identity + human-gate evidence.
