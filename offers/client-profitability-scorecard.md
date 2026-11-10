# Client Profitability Scorecard

- **Status:** Active commercial operating artifact (v0.1)
- **Date:** 2026-11-10
- **Ledger contract:** [../architecture/revenue-ledger-v1.md](../architecture/revenue-ledger-v1.md)
- **Onboarding:** [client-onboarding-playbook.md](client-onboarding-playbook.md)

Use one scorecard per active client/agreement per month. Values without
evidence are `UNVERIFIED`. Do not publish external ROI claims from unverified
rows.

## Scorecard template

| Field | Value | Evidence |
|---|---|---|
| Client | | |
| Agreement / SKU | | Signed SOW |
| Period | YYYY-MM | |
| Setup recognized (USD) | | Invoice |
| MRR billed (USD) | | Invoice |
| Collected cash (USD) | | Bank/provider |
| Direct labor hours | | Timesheet |
| Direct labor cost (USD) | | Rate × hours |
| Model/provider cost (USD) | | Provider bill or UNVERIFIED |
| Cloud/software/connector (USD) | | Invoice allocation |
| Subcontractor (USD) | | Invoice |
| Total direct cost (USD) | | Sum |
| Gross profit (USD) | | Revenue − direct cost |
| Gross margin % | | |
| Support hours used / included | | Ticket log |
| Change orders (count / USD) | | CO records |
| Verified customer outcome | | Baseline vs result pack |
| Margin status | ON_TARGET / WATCH / BREACH | |
| Next action | | Owner + date |

## Guardrails

| Signal | Action |
|---|---|
| Margin < target for 2 consecutive months | Commercial review; scope or price change |
| Support hours > included without CO | Pause discretionary work; issue change order |
| Collected cash lags billed > 30 days | Collections escalation; pause expansion |
| Outcome UNVERIFIED | Do not use in case studies or upsell claims |

## Related

- [aion-outcome-based-services-blueprint.md](aion-outcome-based-services-blueprint.md)
- [change-order-template.md](change-order-template.md)
- [support-tiers.md](support-tiers.md)
