# AION Exposure Verification — Oct 26, 2026

- **Driven by:** TechOps Monday Intelligence Brief (Oct 20–26 window)
- **Related:** AIO-46 customer-zero baseline; AIO-43 SIS v1.1 amendments
- **Method:** Repository and dependency search across workspace repos
  (`aion-core`, `aion-runtime`, `aion-infra`, `aion-data`, `aion-desks`,
  `aion-docs`, `aion-products`). Fail closed on unknowns.
- **Disposition:** Recommended investigation complete for *declared product
  artifacts in this workspace*. Hosted production environments outside this
  checkout still need operator confirmation against live hosts and registries.

## 1. LiteLLM / malicious package 1.107.0

| Check | Result | Evidence |
|---|---|---|
| `litellm` in `requirements*.txt` / `pyproject.toml` / `package.json` | **Not found** | Workspace-wide dependency search 2026-10-26 |
| `litellm` in Dockerfiles / compose | **Not found** | No Python LiteLLM image or service |
| Docs / ADR mentions | **Present (deferred)** | ADR-017 rejects putting LiteLLM proxy in Runtime now; RES-001 marks gateway proxy **DEFER** |
| Affected version `1.107.0` installed | **No evidence in workspace** | No lockfile / image pin references LiteLLM |

**Disposition:** No workspace artifact indicates installation of compromised
LiteLLM 1.107.0. **Not a claim that every historical CI runner or laptop is
clean** — operators should confirm production hosts, GHCR images, and any
experimental Python environments separately. If exposure is ever confirmed:
isolate → investigate with trusted tools → rebuild from verified digests →
rotate potentially accessible credentials.

## 2. PHP emergency RCEs (CVE-2026-109618 / CVE-2026-109619)

| Check | Result | Evidence |
|---|---|---|
| PHP runtime / `composer.json` / php-fpm in product repos | **Not found** | No product PHP stack under managed repos |
| Internet-facing PHP applications owned by AION product code | **None declared** | N/A in this workspace |

**Disposition:** AION product/infrastructure code in this workspace is not a
PHP application estate. Client websites remain in scope for the **Emergency
Web Infrastructure Review** commercial module (SIS-WEB-*).

## 3. Certificate / trust dependencies (eMudhra / intermediate CA risk)

| Check | Result | Evidence |
|---|---|---|
| Named Certificate & Trust Dependency Inventory | **Gap** | Expiry check `CA-EXP-02` exists; no CA/issuer inventory artifact |
| Managed TLS / ACME in infra | **Partial** | Traefik/ACME and DB TLS documented in `aion-infra`; DR drills include cert issuance |
| Explicit eMudhra / blocked-intermediate inventory | **Not found** | No inventory of OS/browser trust-store exceptions |

**Disposition:** No evidence AION issued customer certs via affected eMudhra
intermediates (and vendor statements claim those intermediates issued none).
**Gap:** AION still lacks a formal trust-store / issuer inventory and DR
assertion that blocked intermediates are removed. Track under SIS-TR-* and
`CA-TR-*`.

## 4. Supply-chain posture snapshot (related)

| Control area | Status | Notes |
|---|---|---|
| Digest-pinned production images (VPS path) | **Met (path)** | `deploy-vps.yml` resolves `@sha256:` and refuses `:latest` |
| Lockfiles | **Met** | `package-lock.json` / Terraform provider locks |
| Container image vuln scan gate | **Gap** | `aion-infra/policies/README.md` — accepted Phase 3 limitation |
| GitHub Actions pin to full commit SHA | **Partial** | Many Actions use major tags (`@v4`); some (e.g. ssh-action) use full SHA |
| Documented rollback | **Partial** | Deploy scripts support previous digest; formal Trust Gate runbook still thin |

## 5. Ransomware / SafePay-relevant controls (pointer)

Customer-zero baseline (2026-10-06) remains **Assist-ready** with hard stops on
Identity and Credentials. This verification does **not** re-score A–K. Next
assurance work should exercise ransomware resilience checks (`CA-BK-03/04`,
`CA-RR-01`) against AION backups and revoke paths before selling the module.

## 6. Record

| Field | Value |
|---|---|
| Verified by | Cloud agent execution of Oct 26 brief sprint |
| Date (America/New_York) | 2026-10-26 |
| Follow-on | SIS v1.1 · SEL financial-action · CA catalog · operator live-host confirmation |
