# Accounting AI & Automation Governance Policy Template

Companion to [Secure Accounting AI Foundation](techops-secure-accounting-ai-foundation.md)
(Linear **AIO-50**). Clients adapt this template; it is not legal advice.

**Owner:** _________________ **Effective:** ________ **Review cadence:** quarterly  
**SIS version:** v1.0 **Related audit:** Governance & Security Infrastructure Audit v2

---

## 1. Purpose

Define acceptable use of AI tools, automations, and agents that touch client
documents, tax/accounting SaaS, email, or payment information for
**[Firm Name]**.

## 2. Scope

Applies to all staff, contractors, and non-human identities (service accounts,
integrations, agents) that access firm or client data.

## 3. Identity & access

1. Phishing-resistant MFA (or equivalent) is required for admin and for anyone
   accessing client tax / financial documents.
2. Shared mailboxes are not standing identities for humans or agents.
3. Every production agent has a registry entry: owner, purpose, tenant,
   permission tier, tools, data scope, approval rules, revocation state.
4. Offboarding revokes human and machine access within 24 hours.

## 4. Data & document boundaries

1. Client documents live only in approved systems (list: _______________).
2. Classification at minimum: public / internal / confidential / regulated.
3. No client confidential/regulated data in personal AI accounts, unmanaged
   chat tools, or shadow SaaS.
4. Export / download of regulated client data requires business need and logging.

## 5. AI acceptable use

| Allowed (with firm accounts) | Forbidden |
|---|---|
| Summarize internal procedures | Paste client SSNs / full returns into consumer AI |
| Draft internal checklists | Unattended tax filing or payment initiation |
| Assist research on public guidance | Training public models on client data |
| Observe/Assist agents under registry | Agents with standing Execute on money moves |

Shadow-AI discovery is performed at least quarterly (Audit G3).

## 6. Permission tiers

| Tier | Meaning | Examples |
|---|---|---|
| Observe | Read / recommend only | Read ticket status |
| Assist | Draft; human sends/commits | Draft client email |
| Execute | Allow-listed writes | Create task in approved system |
| Human-gated | Exact-payload approval | Wires, refunds, e-file submit, vendor bank-change |

Financial workflows default to **Assist** or **Human-gated**. Execute requires
written allow-list and Audit hard-stop clearance.

## 7. Approvals & dual control

The following always require a named human approver (dual control where noted):

- Payment initiation / wires / ACH (dual control)
- Vendor bank-detail changes (dual control)
- Tax filing submission
- Bulk client data export
- Production agent permission expansion

Approvals record: actor, payload summary, time, decision, evidence link.

## 8. Credentials & secrets

1. Maintain a Credential & Token Inventory (human / service / agent).
2. No long-lived line-of-business API keys in agent runtimes.
3. Rotation and revocation mechanisms documented per credential class.
4. Secrets are not stored in shared inboxes or spreadsheets.

## 9. Logging, incidents, recovery

1. Agent and automation runs must be attributable (who/what/when/client).
2. Suspected compromise: revoke sessions/tokens, disable agent registry entries,
   notify owner _______________.
3. Backups cover identity, cloud files, and accounting SaaS exports per vendor
   RPO; restore tested at least every 90 days.

## 10. Exceptions

Exceptions are written, time-bounded, owned, and reviewed. Unowned exceptions
are invalid. Register location: _______________.

## 11. Acknowledgement

I understand this policy and will not bypass human gates or data boundaries.

Name _____________ Role _____________ Date _____________

---

## Mapping to TechOps delivery

| Policy section | Delivery step (Foundation SKU) |
|---|---|
| §3 Identity | MFA + registry |
| §4–5 Data / AI use | Document boundaries + acceptable-use |
| §6–7 Tiers / approvals | Secure AI Execution gates |
| §8 Credentials | Credential & Token Inventory |
| §9 Logging / recovery | Evidence path + backup proof |
