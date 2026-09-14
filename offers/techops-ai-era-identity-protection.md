# TechOps Foundation — AI-Era Identity Protection

Foundation SKU component (Linear AIO-21; TechOps Monday Brief 2026-09-14).

**Stop selling “MFA setup.” Sell identity hardening.**

AI adoption expands the phishing surface (fake Copilot/ChatGPT/Claude updates,
AiTM token theft, malicious “AI installers”). Identity is the first control in
the [Security & Agent Readiness Audit](techops-security-agent-readiness-audit.md).

## SKU one-liner

> Harden who can sign in, what apps can consent, which devices can access mail
> and files, and how suspicious sessions are stopped — before any agent
> automation is switched on.

## Component stack (delivery order)

| Step | Control | Outcome |
|---|---|---|
| 1 | Phishing-resistant MFA / passkeys (admins + finance first) | Credential stuffing / basic phishing blocked |
| 2 | Endpoint protection on workstations + servers | Malware / fake installer path reduced |
| 3 | Email link + attachment protection | AI-themed lure delivery reduced |
| 4 | OAuth / app-consent restrictions (admin approval) | Token theft via malicious apps constrained |
| 5 | Device compliance / MDM for company data access | Unmanaged devices cannot reach mail/files |
| 6 | Suspicious-session response playbook | AiTM / token replay detected and revoked |

## Sales / delivery checklist

### Discovery

- [ ] Admin + finance accounts listed; MFA method documented
- [ ] Shared mailbox / shared password practices identified
- [ ] Current OAuth apps / consent grants inventoried
- [ ] Endpoint coverage % known (or scored 0 in audit)
- [ ] Who responds to “unusual sign-in” today?

### Implementation

- [ ] Enforce MFA (prefer passkeys / phishing-resistant) for privileged roles
- [ ] Enforce MFA for all standard users (not optional)
- [ ] Restrict user consent; require admin approval for apps
- [ ] Deploy endpoint protection + disk encryption baseline
- [ ] Enable link/attachment protection on mail
- [ ] Enroll devices in compliance policy before data access
- [ ] Document session revoke steps (IdP + mail + SaaS)

### Acceptance

- [ ] Privileged sign-in without compliant MFA fails
- [ ] Unapproved OAuth app cannot be user-consented
- [ ] Non-compliant device blocked from mail/files
- [ ] Tabletop: suspicious session → revoke → notify owner &lt; 1 hour

## Accounting-firm premium posture

Tax / bookkeeping firms face FTC Safeguards expectations around customer
information and MFA. Package Identity Protection as **mandatory Foundation**,
not an optional add-on:

| Control | Accounting emphasis |
|---|---|
| MFA / passkeys | All staff touching client tax docs |
| Consent restrictions | Block shadow “AI bookkeeping” OAuth apps |
| Email protection | Wire-change / W-2 / IRS-themed lures |
| Session response | Compromised mailbox = payment instruction fraud |
| Device compliance | No personal unmanaged laptops on client files |

Pricing implication: recurring security component is justified — you are
protecting regulated workflows, not just productivity.

## Audit mapping (Identity domain A1–A5)

| Audit control | This SKU step |
|---|---|
| A1 Phishing-resistant MFA (admin/finance) | Step 1 |
| A2 MFA enforced for standard users | Step 1 |
| A3 OAuth / app-consent restricted | Step 4 |
| A4 Privileged accounts separated | Step 1 + delivery hygiene |
| A5 Suspicious-session response | Step 6 |

Hard stop from the audit: Identity &lt; 8 → **not Execute-ready** for agents.

## Dependencies / non-goals

- **Depends on:** client IdP (M365/Google) admin access.
- **Does not include:** full SOC, custom SIEM build, or agent automation
  (those are Secure Automation / Security Ops Lite).
- **Pairs with:** [action-tiers.md](../governance/action-tiers.md) — customer-facing
  AI still must not receive unrestricted system authority after identity is hardened.
