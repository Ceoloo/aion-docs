# Financial-Action Policy (cross-vertical)

- **Status:** Active guidance (Oct 26, 2026 TechOps Monday Intelligence Brief)
- **SIS map:** SIS-GX-11 · SIS-AG-04 · SIS-GX-06
- **SEL map:** [secure-execution-layer-v1.md](../architecture/secure-execution-layer-v1.md) §2a
- **Vertical handoffs:** Accounting AIO-50 · Dealer/rental AIO-51 · Electrical AIO-49

Major U.S. financial institutions are restricting unauthorized third-party AI
agents from accessing customer accounts or performing transactions. AION treats
that as an **architecture boundary**, not a temporary product constraint.

## Hard rule

An agent may **prepare** financial work. It may **not** independently
authenticate to a bank (or payment rail) with shared customer credentials and
execute money movement without an explicit, recorded human authorization path.

```text
Observe → Analyze → Prepare → Human Approve → Authorized Execution → Verify
```

| Stage | Allowed | Forbidden |
|---|---|---|
| Observe | Read allowed books, invoices, inventory, statements (in scope) | Silent scraping of bank portals with shared passwords |
| Analyze | Classify, reconcile candidates, flag exceptions | Autonomously changing vendor bank details |
| Prepare | Draft payments, filings, refunds, financing packets | Submitting without human approval |
| Human Approve | Named approver, exact payload, dual control where required | Agent holding ambient `approve` |
| Authorized Execution | Official APIs / delegated permissions via gateway | Credential stuffing, browser RPA on bank MFA |
| Verify | Match ledger/evidence to intended outcome | Assuming success without confirmation |

Use officially supported integrations and delegated permissions. Prefer
short-lived / vaulted credentials (SIS-GX-07). Avoid credential-sharing and
unsupported account automation.

## Vertical defaults

| Vertical | Prioritize first | Keep human-gated |
|---|---|---|
| Accounting | Document processing, reconciliation prep, exception ID | Wires, ACH, tax filing, vendor bank changes |
| Dealers / rentals | Lead handling, appointments, inventory inquiries, ops reporting | Financing submit, deposits, refunds, voids, contracts |
| Electrical | Quote prep, material availability, project updates, purchasing approvals | AP/AR payment initiation, contract/change-order money moves |

## Commercial packaging

Separate **ordinary workflow automation** from **high-assurance financial
execution** in pricing and implementation scope. Selling “AI that pays bills”
without SIS-GX-11 evidence is out of policy.

## Related

- [action-tiers.md](action-tiers.md)
- [human-gates.md](human-gates.md)
- [risk-levels.md](risk-levels.md)
- [../offers/techops-accounting-ai-governance-policy-template.md](../offers/techops-accounting-ai-governance-policy-template.md)
