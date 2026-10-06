# AION Customer-Zero Security Baseline Assessment

- **Issue:** AIO-46
- **Date:** 2026-10-06
- **Assessor:** TechOps / Security Infrastructure Layer (AION as customer zero)
- **Standard:** [Security Infrastructure Standard v1](../architecture/security-infrastructure-standard-v1.md)
- **Scorecard:** [techops-security-agent-readiness-audit.md](techops-security-agent-readiness-audit.md)
- **Depends on:** AIO-43 (Done), AIO-44 (Registry), AIO-45 (SEL v1)

Apply the TechOps Security & Agent Readiness Audit to **AION itself**. Unknown
controls score **0** (fail closed).

## 1. Domain scores (0–3 per control)

### A. Identity — **9 / 15**

| # | Score | Notes / evidence |
|---|---|---|
| A1 | 2 | Privileged ops use MFA practices; no IdP export in-repo (ops evidence pending) |
| A2 | 2 | Workforce MFA expected; not continuously attested here |
| A3 | 2 | OAuth consent restricted in doctrine; IdP screenshot not attached |
| A4 | 2 | No shared-mailbox standing admin in platform design |
| A5 | 1 | Session revoke playbook partial; agent contain path via registry (AIO-44) |

### B. Devices — **4 / 12**

| # | Score | Notes |
|---|---|---|
| B1 | 1 | Endpoint coverage tribal / not inventoried in this audit |
| B2 | 1 | MDM posture not evidenced for all company data paths |
| B3 | 1 | Disk encryption assumed on engineer laptops; no attestation export |
| B4 | 1 | Local admin removal not proven estate-wide |

### C. Network — **4 / 12**

| # | Score | Notes |
|---|---|---|
| C1 | 1 | Segmentation not fully documented for all environments |
| C2 | 1 | Remote admin path hardening partial |
| C3 | 1 | DNS/web filtering not evidenced |
| C4 | 1 | Firewall change ownership logging incomplete |

### D. Cloud — **6 / 12**

| # | Score | Notes |
|---|---|---|
| D1 | 2 | Tenant hardening checklists exist in infra docs; not fully scored |
| D2 | 2 | Sharing defaults restricted in practice for core repos |
| D3 | 1 | Standing admin count not JIT-proven |
| D4 | 1 | 180-day audit log retention not evidenced for all clouds |

### E. Data — **7 / 12**

| # | Score | Notes |
|---|---|---|
| E1 | 2 | Classes named in SIS / governance |
| E2 | 2 | Least privilege in Core policy; product paths still migrating |
| E3 | 2 | CRM/PII locations known for Revenue paths |
| E4 | 1 | DLP estate-wide not proven |

### F. Backup / recovery — **5 / 12**

| # | Score | Notes |
|---|---|---|
| F1 | 2 | Backups discussed (AIO-7 related); not fully Met |
| F2 | 1 | Immutable/offline copy not evidenced |
| F3 | 1 | Restore test &lt;90d not attached |
| F4 | 1 | RPO/RTO documented partially |

### G. SaaS — **6 / 12**

| # | Score | Notes |
|---|---|---|
| G1 | 2 | Partial SaaS inventory |
| G2 | 1 | Offboarding ≤24h not evidenced |
| G3 | 2 | Shadow AI policy emerging (SIS-SA-03) |
| G4 | 1 | Quarterly vendor review cadence not proven |

### H. Automation — **7 / 12**

| # | Score | Notes |
|---|---|---|
| H1 | 2 | Integrations partially inventoried; MCP/GHL known |
| H2 | 3 | **Met** — secrets-not-in-git hard rule + hygiene |
| H3 | 1 | Change control for prod automations incomplete |
| H4 | 1 | Failure alert → owner path partial |

### I. AI / Agents — **8 / 15**

| # | Score | Notes / evidence |
|---|---|---|
| I1 | 2 | Registry schema + API + inventory (AIO-44); prod registration incomplete |
| I2 | 2 | ADR-007 tiers; not enforced on every product path yet |
| I3 | 2 | R3 human gates in Core/Runtime; coverage gaps on parallel planes |
| I4 | 2 | PolicyEngine fail-closed for tools/data; legacy paths remain |
| I5 | 2 | Trust Score + executions; operator UX still maturing |

### J. Observability — **7 / 12**

| # | Score | Notes |
|---|---|---|
| J1 | 1 | Centralized alert review cadence not evidenced |
| J2 | 2 | Incident path exists culturally; samples incomplete |
| J3 | 3 | **Strong** — executions/events attributable (who/what/when/tenant) |
| J4 | 1 | Cost visibility partial (execution cost fields; not estate-wide) |

### K. Credentials & tokens — **5 / 15**

| # | Score | Notes |
|---|---|---|
| K1 | 1 | Credential & Token Inventory **missing** as owned artifact |
| K2 | 1 | Owner/system/scope incomplete |
| K3 | 1 | Expiration/storage not systematically recorded |
| K4 | 1 | Rotation/revocation documented partially (registry revoke for agents) |
| K5 | 1 | Doctrine forbids LoB keys in agent runtimes; isolation still Partial |

### L. Connected infrastructure (informational) — **2 / 9**

Discovery only; not in automation-readiness formula. Minimal OT/IoT adjacency
for AION SaaS ops — score low and out of scope for Execute decisions.

## 2. Totals

| Domain | Max | Score |
|---|---|---|
| Identity | 15 | **9** |
| Devices | 12 | **4** |
| Network | 12 | **4** |
| Cloud | 12 | **6** |
| Data | 12 | **7** |
| Backup | 12 | **5** |
| SaaS | 12 | **6** |
| Automation | 12 | **7** |
| AI / Agents | 15 | **8** |
| Observability | 12 | **7** |
| Credentials & tokens | 15 | **5** |
| **Total (A–K)** | **141** | **68** |
| Connected-infra (L) | 9 | 2 (informational) |

### Automation readiness

```
readiness = Identity + Credentials + Automation + AI/Agents + Observability
         = 9 + 5 + 7 + 8 + 7 = 36 / 69
```

**Band: Assist-ready (23–45).**  
Observe/Assist OK with supervision; **no unattended Execute** until hard-stops
and credential inventory improve.

**Hard-stop check:** Identity 9 ≥ 8 (pass) · Credentials 5 &lt; 6 (**FAIL**) ·
I3 = 2 ≠ 0 (pass).  
→ **Cannot be Execute-ready** until Credentials ≥ 6 and remaining gaps close.

## 3. Top 10 risks / gaps

1. **No owned Credential & Token Inventory (K1–K5)** — blast radius unknown across SaaS/MCP/CRM.
2. **Parallel execution planes (O2/O3)** — legacy Revenue Copilot / Action Engine bypass gateway.
3. **Production agents not all registry-complete** — AIO-44 schema ready; registration incomplete.
4. **Long-lived LoB credentials may still reach agent processes** — SIS-GX-07 Partial.
5. **Backup restore evidence missing** — ransomware / DR confidence low (AIO-7).
6. **Endpoint / MDM estate not attested** — device domain scores ≤1.
7. **Contain runbook not tabletop-proven end-to-end** — registry revoke exists; IdP+vault orchestration open.
8. **Rate/spend limits recommended only** — runaway cost residual (SIS-GX-09).
9. **Cloud audit log retention ≥180d not evidenced** for all tenants.
10. **Offboarding SaaS ≤24h not evidenced** — standing access risk.

## 4. Hard-stop findings

| ID | Finding | Blocks |
|---|---|---|
| HS-1 | Credentials domain &lt; 6 | Execute-ready band |
| HS-2 | Ungoverned parallel control planes with possible LoB keys | Execute-tier production expansion |
| HS-3 | Incomplete production Agent Identity Registry registration | SIS-AG-02 / Execute under auth=required |

## 5. 30 / 60 / 90 remediation plan

### 30 days

- Complete Credential & Token Inventory (domain K) for AION ops + agents.
- Register all §1 agents from
  [agent-identity-registry-inventory.md](../governance/agent-identity-registry-inventory.md)
  in staging + production via `/v1/registry/*`.
- Auth mode=`required` on non-local Runtime deployments.
- Tabletop: revoke agent → command denied → reactivate (SIS-AG-09).
- Kill/disable parallel O2 path credentials or put behind gateway.

### 60 days

- Adapter credential isolation (no long-lived LoB keys in agent runtimes).
- Backup restore test + RPO/RTO owners (close AIO-7 evidence).
- Device/MDM attestation sample for engineer estate.
- Cloud audit retention proof ≥180 days.
- Rate/spend limits on Execute paths (elevate GX-09).

### 90 days

- Retire or wrap remaining parallel planes (O3).
- Continuous Assurance connectors prototype (AIO-47).
- Re-score customer-zero; target **Execute-ready (gated)** band (≥46 readiness)
  with Credentials ≥ 6 and zero HS findings.
- Package remediated controls as client case-study proof (below).

## 6. Evidence links (controls already verified)

| Control | Evidence |
|---|---|
| Secrets not in git | Repo hygiene + CI; SIS §6 Met |
| Execution Gateway ingress | ADR-003; `aion-runtime` `/v1/*` |
| Identity plane | ADR-005; Runtime auth |
| Observe/Assist/Execute contracts | ADR-007; PolicyEngine tests |
| Authority / provenance | ADR-008 |
| Agent Identity Registry | AIO-44 PRs: core/data/runtime + inventory doc |
| Secure Execution Layer contract | ADR-012 + `secure-execution-layer-v1.md` |
| Attributable executions | `executions` + Trust Score |
| FeatureGate kill-switch | Core FeatureGate tests |

## 7. Client case-study candidates (after remediation)

After 30/60/90 closure, these can become external proof points:

1. **Agent Identity Registry + revoke path** — from Gap → Met with inventory export.
2. **Gateway fail-closed policy** — Observe cannot Execute; tenant isolation proofs.
3. **Credential & Token Inventory discipline** — human/service/agent ownership classes.
4. **Customer-zero scored audit method** — this document’s scorecard application.
5. **Assist-ready → Execute-ready (gated) journey** — band movement with evidence.

## 8. Acceptance (AIO-46)

| Criterion | Status |
|---|---|
| Score by domain | §1–§2 |
| Top 10 risks/gaps | §3 |
| Hard-stop findings | §4 |
| Automation readiness band | §2 — **Assist-ready** |
| 30/60/90 remediation plan | §5 |
| Evidence links | §6 |
| Case-study candidates post-remediation | §7 |
