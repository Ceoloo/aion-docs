# TechOps Security & Agent Readiness Audit

Sales front door for TechOps (Linear AIO-18; updated TechOps Monday Brief
2026-10-05). Control baseline:
[../architecture/security-infrastructure-standard-v1.md](../architecture/security-infrastructure-standard-v1.md).

This audit **does not install tools**. It scores readiness to operate autonomous
digital labor safely, then maps findings to the three TechOps layers:

1. **Secure Digital Foundation** — human identity, devices, cloud/SaaS, email,
   network, backups, secrets, recovery  
2. **Secure AI Execution** — agent identity, Agent Registry, gateway,
   Observe/Assist/Execute, credential isolation, human approvals, audit
   evidence, kill/revoke  
3. **Continuous Assurance** — exposure → identity/permission drift →
   vulnerability → backup health → agent behavior → remediation → evidence  

Vertical overlays (Contractor / Accounting / Automotive) specialize controls;
they do **not** create separate architectures.

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
**Domain totals** below. **Automation readiness** derived from Identity +
Credentials + Automation + AI/Agents + Observability (see formula).

## Scorecard

### A. Identity (weight: critical) — Human identity

| # | Control | 0 | 1 | 2 | 3 | Notes / evidence | SIS |
|---|---|---|---|---|---|---|---|
| A1 | Phishing-resistant MFA / passkeys on admin + finance | | | | | | SIS-HI-01 |
| A2 | Standard users MFA enforced (not optional) | | | | | | SIS-HI-02 |
| A3 | OAuth / app-consent restricted; admin approval required | | | | | | SIS-HI-03 |
| A4 | Privileged accounts separated; no shared mailboxes as identity | | | | | | SIS-HI-04 |
| A5 | Suspicious-session response playbook (AiTM / token theft) | | | | | | SIS-HI-05 |

**Identity subtotal:** __ / 15

### B. Devices

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| B1 | Endpoint protection on all workstations / servers | | | SIS-DV-01 |
| B2 | Device compliance / MDM for company data access | | | SIS-DV-02 |
| B3 | Disk encryption + screen lock policy | | | SIS-DV-03 |
| B4 | Local admin rights removed for standard users | | | SIS-DV-04 |

**Devices subtotal:** __ / 12

### C. Network

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| C1 | Segmented guest / IoT vs corporate | | | SIS-NW-01 |
| C2 | VPN or ZTNA for remote admin access | | | SIS-NW-02 |
| C3 | DNS / web filtering for malware & phishing | | | SIS-NW-03 |
| C4 | Firewall change ownership + logging | | | SIS-NW-04 |

**Network subtotal:** __ / 12

### D. Cloud (M365 / Google / hyperscaler)

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| D1 | Tenant hardening baseline applied | | | SIS-CL-01 |
| D2 | External sharing defaults least privilege | | | SIS-CL-02 |
| D3 | Admin roles JIT / limited standing privilege | | | SIS-CL-03 |
| D4 | Audit logs retained ≥ 180 days | | | SIS-CL-04 |

**Cloud subtotal:** __ / 12

### E. Data

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| E1 | Data classes identified (public / internal / confidential / regulated) | | | SIS-DA-01 |
| E2 | Access follows least privilege (not “everyone in SharePoint”) | | | SIS-DA-02 |
| E3 | Customer/PII / tax / financing data locations mapped | | | SIS-DA-03 |
| E4 | DLP or equivalent on mail + files for sensitive classes | | | SIS-DA-04 |

**Data subtotal:** __ / 12

### F. Backup / recovery

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| F1 | Backups for identity + cloud files + line-of-business | | | SIS-BK-01 |
| F2 | Immutable / offline copy against ransomware | | | SIS-BK-02 |
| F3 | Restore tested in last 90 days | | | SIS-BK-03 |
| F4 | RPO/RTO documented and owned | | | SIS-BK-04 |

**Backup subtotal:** __ / 12

### G. SaaS

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| G1 | Inventory of SaaS with business data | | | SIS-SA-01 |
| G2 | Offboarding revokes SaaS within 24h | | | SIS-SA-02 |
| G3 | No shadow AI tools with customer data (policy + discovery) | | | SIS-SA-03 |
| G4 | Vendor access / integrations reviewed quarterly | | | SIS-SA-04 |

**SaaS subtotal:** __ / 12

### H. Automation

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| H1 | Integrations inventory (Zapier/Make/n8n/native/MCP) with owners | | | SIS-AU-01 |
| H2 | Secrets not in shared inboxes / spreadsheets | | | SIS-AU-02 |
| H3 | Change control for production automations | | | SIS-AU-03 |
| H4 | Failure alerts + human owner for broken flows | | | SIS-AU-04 |

**Automation subtotal:** __ / 12

### I. AI / Agents (AION readiness) — Secure AI Execution

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| I1 | Agent identities distinct from shared user logins; registry fields complete | | | SIS-AG-01/02 |
| I2 | Observe / Assist / Execute tiers declared per workflow | | | SIS-AG-03 |
| I3 | Human gates on financial / destructive / regulated actions | | | SIS-AG-04 |
| I4 | Permissions + data-class allow-lists enforced (not prompt-only) | | | SIS-AG-05 |
| I5 | Execution Trust Score / audit trail available to operators | | | SIS-AG-06 |

**Mandatory registry fields (fail I1 if missing):** `agent_id`, `human_owner`,
`business_purpose`, `tenant`, `permission_tier`, `tools`, `data_scope`,
`delegated_authority`, `policy_version`, `execution_evidence`,
`revocation_state`.

**AI/Agents subtotal:** __ / 15

### J. Observability

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| J1 | Centralized auth / mail / endpoint alerts reviewed | | | SIS-OB-01 |
| J2 | Incident record + owner notification path | | | SIS-OB-02 |
| J3 | Automation/agent runs attributable (who/what/when/tenant) | | | SIS-OB-03 |
| J4 | Cost / usage visibility for AI and integrations | | | SIS-OB-04 |

**Observability subtotal:** __ / 12

### K. Credentials & tokens — Human + Machine Identity

Do not sell password cleanup alone. Inventory covers Zapier tokens, n8n
credentials, CRM OAuth grants, Google/Microsoft tokens, Shopify tokens,
accounting integrations, webhook secrets, and MCP credentials.

| # | Control | 0–3 | Evidence | SIS |
|---|---|---|---|---|
| K1 | Credential & Token Inventory exists (owned) | | | SIS-CT-01 |
| K2 | Owner, system, scope populated for every row | | | SIS-CT-02 |
| K3 | Expiration + storage location recorded | | | SIS-CT-03 |
| K4 | Rotation + revocation mechanisms documented | | | SIS-CT-04/05 |
| K5 | Ownership class labeled (`human` / `service` / `agent`); no long-lived LoB keys in agent runtimes | | | SIS-CT-06/07 |

**Inventory columns (mandatory):** owner · system · scope · expiration ·
storage_location · rotation_mechanism · revocation_mechanism ·
ownership_class.

**Credentials subtotal:** __ / 15

### L. Connected infrastructure readiness (discovery only)

Not an implementation promise. Score discovery maturity for OT/IoT / connected
equipment conversations (especially contractors). Do **not** sell OT
cybersecurity services from this row alone.

| # | Control | 0–3 | Evidence |
|---|---|---|---|
| L1 | Connected devices / IoT / OT adjacency inventoried (office vs field/plant) | | |
| L2 | Remote access paths to connected equipment documented | | |
| L3 | Explicit “out of scope / next phase” decision recorded | | |

**Connected-infra discovery subtotal:** __ / 9 (reported separately; not in
automation-readiness formula)

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
| Credentials & tokens | 15 | |
| **Total (A–K)** | **141** | |
| Connected-infra discovery (L) | 9 | *(informational)* |

### Automation readiness (derived)

```
readiness = Identity + Credentials + Automation + AI/Agents + Observability
max = 15 + 15 + 12 + 15 + 12 = 69
```

| Band | Range | Meaning |
|---|---|---|
| **Not ready** | 0–22 | Do not deploy Execute-tier agents; Foundation first |
| **Assist-ready** | 23–45 | Observe/Assist OK with supervision; no unattended Execute |
| **Execute-ready (gated)** | 46–58 | Execute allow-lists + R3 gates; monitor Trust Scores |
| **Managed autonomy candidate** | 59–69 | Candidate for earned autonomy + Continuous Assurance |

**Hard stop:** Identity &lt; 8 **or** Credentials &lt; 6 **or** AI/Agents I3 = 0
→ cannot be Execute-ready regardless of total.

## Deliverable template (client output)

1. **Current risk** — top 5 findings by blast radius (prefer Identity,
   Credentials, Data, Backup, AI Execute paths).
2. **Automation readiness** — band + hard-stop flags.
3. **Recommended architecture** — map gaps → Secure Digital Foundation /
   Secure AI Execution / Continuous Assurance.
4. **Implementation roadmap** — 30 / 60 / 90 day ordered work.
5. **Monthly managed-services requirement** — what AION operates vs client owns.

### Vertical packaging notes

| Vertical | Emphasize | Offer language |
|---|---|---|
| Electrical | Job docs, estimates, field devices, AP/AR; OT/IoT as discovery | **Secure Digital Contractor Infrastructure** |
| Accounting | Identity hardening, document/data boundaries, financial-action approvals, agent governance, immutable evidence | **Secure Accounting AI Foundation** |
| Dealer / rental | PII, financing, DMS; authority bands | Inventory/search → communication → CRM ops → **financial/contract (human-gated)** |

#### Dealer authority bands (reference)

| Band | Scope | Default |
|---|---|---|
| 1 | Inventory / search | Observe |
| 2 | Communication | Assist |
| 3 | CRM operations | Execute (allow-listed) |
| 4 | Financial / contract actions | Human-gated |

## SKU mapping cheat sheet

| Finding cluster | Layer / SKU |
|---|---|
| MFA, consent, sessions, devices | Secure Digital Foundation — [AI-Era Identity Protection](techops-ai-era-identity-protection.md) |
| Backup, cloud hardening, network | Secure Digital Foundation |
| Token/OAuth/MCP credential chaos | Foundation + Credential inventory (Human + Machine Identity) |
| Integration chaos, agent identity, gateway gaps | Secure AI Execution |
| No Trust Score / no gates / Execute sprawl | Secure AI Execution |
| Alert fatigue / no response / drift | [Security Operations Lite](techops-security-operations-lite.md) → Continuous Assurance (AIO-47) |

## Invariants

- Audit evidence beats screenshots of tool logos.
- Unknown scores **0**, not “assumed fine.”
- Execute-tier recommendations require Identity + Credentials + human-gate evidence.
- Agent Identity Registry + Secure Execution Layer are **required infrastructure**,
  not optional advanced features.
- Declare which [Security Infrastructure Standard](../architecture/security-infrastructure-standard-v1.md)
  version the score was taken against.
