# Authentication Path Verification

- **Status:** Active TechOps procedure (Secure Digital Foundation)
- **Date:** 2026-11-09
- **Brief:** [../.aion/research/techops-monday-brief-2026-11-09.md](../.aion/research/techops-monday-brief-2026-11-09.md)
- **Foundation SKU:** [techops-ai-era-identity-protection.md](techops-ai-era-identity-protection.md)
- **SIS map:** SIS-HI-01/02 · SIS-NW-02 · CA-ID-* · CA-RR-01
- **Trigger example:** SonicWall SSL VPN advisory / CVE-class remote-access MFA bypass (actively exploited paths)

**MFA documented ≠ MFA enforced on every login path.**

Authentication Path Verification tests whether alternate authentication methods,
legacy VPN profiles, or local accounts undermine the intended MFA requirement.

## SKU one-liner

> Prove that every remote-access and privileged login path actually enforces the
> MFA and identity controls the customer believes are in place — then remediate
> the paths that don’t.

## When to run

| Cadence | Trigger |
|---|---|
| **Intake** | Secure Digital Foundation / Audit v2 identity + network domains |
| **Quarterly** | Continuous Assurance remote-access review |
| **Urgent** | Vendor advisory, CISA KEV entry, or customer VPN/firewall CVE affecting auth |

## Procedure

### 1. Inventory authentication paths

List every way a human or service can obtain a session into corporate systems:

- IdP interactive login (SAML / OIDC / passkey)
- SSL VPN / ZTNA / IPSec
- RDP / jump hosts / vendor remote tools
- Local firewall / appliance admin accounts
- “Break glass” and shared emergency accounts
- App-specific passwords / legacy IMAP / API keys used as login substitutes

### 2. Map method → MFA expectation

| Path | Intended control | Actual control (tested) | Gap |
|---|---|---|---|
| … | SAML + MFA | … | … |

Record whether an alternate method (local DB, LDAP, RADIUS, certificate-only,
saved password, older portal) can succeed **without** the IdP MFA challenge.

### 3. SonicWall / firewall-specific checks (when in scope)

For affected Gen 7 / Gen 8 class appliances (example advisory window Nov 2026):

- [ ] Firmware family is on vendor-fixed release (e.g. SonicOS 7.3.4 / 8.2.3
      families or newer fixed builds — confirm against current vendor advisory)
- [ ] SSL VPN enabled methods inventoried
- [ ] SAML/MFA required path verified with a test user
- [ ] Alternate auth methods tested for bypass of SAML/MFA requirement
- [ ] Admin UI remote exposure reviewed (internet-facing admin = critical)
- [ ] Compromise investigation decision recorded if exposure warrants it

Prefer **vendor-supported remediation** over compensatory folklore. Urgent
reviews sit outside the quarterly cadence.

### 4. Evidence required

| Evidence | Example |
|---|---|
| Firmware / IdP version | Screenshot or API export with timestamp |
| Path test matrix | Pass/fail per method |
| Bypass attempt result | Ticket / video / log excerpt (redacted) |
| Remediation | Patch change record + retest |
| Residual risk | Paths deferred with owner + date |

Unknown / untested paths score **0** (fail closed) on the audit.

## Acceptance

- [ ] Every remote-access path is inventoried or explicitly out of scope
- [ ] At least one negative test shows non-compliant MFA path is blocked **or**
      the gap is logged as open critical/high with owner
- [ ] Privileged break-glass accounts have compensating monitoring + owner
- [ ] Retest scheduled after remediation
- [ ] Findings feed Continuous Assurance (`CA-ID-*`, `CA-NW-*`, `CA-RR-01`)

## Packaging impact

| Layer | Change |
|---|---|
| **Secure Digital Foundation** | Procedure included in identity + network delivery |
| **Continuous Assurance** | Quarterly remote-access authentication review; urgent CVE-driven reviews |
| **Verticals** | Prioritize accounting remote staff, contractor job-site VPN, multi-site dealers/rentals |

## Explicit non-claims

- This procedure is not a penetration-test substitute for full red-team scope.
- Naming a CVE in a brief does not assert a specific customer is vulnerable.
- Compensating controls do not replace vendor fixes when fixes exist.

## Related

- [techops-security-agent-readiness-audit.md](techops-security-agent-readiness-audit.md)
- [../architecture/continuous-assurance-v1.md](../architecture/continuous-assurance-v1.md)
- [../architecture/security-infrastructure-standard-v1.md](../architecture/security-infrastructure-standard-v1.md)
