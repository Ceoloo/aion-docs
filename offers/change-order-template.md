# Change Order Template

- **Status:** Active template (v0.1)
- **Date:** 2026-11-10
- **Parent SOW:** [implementation-scope-template.md](implementation-scope-template.md)
- **Risk map:** [../governance/business-action-risk-classification.md](../governance/business-action-risk-classification.md)

No out-of-scope delivery without a signed change order (CO). Verbal requests
are intake only.

## Change order

| Field | Value |
|---|---|
| CO id | CO-[YYYYMMDD]-[nn] |
| Client / agreement id | |
| Requested by | |
| Date requested | |
| AION owner | |
| Related ticket / Linear | |

### 1. Requested change

Describe the delta from the signed SOW (systems, workflows, volumes, locations,
reporting, integrations).

### 2. Reason

Business need / dependency / discovery finding.

### 3. Impact

| Dimension | Impact |
|---|---|
| Scope | add / remove / modify |
| Schedule | [days] |
| Setup fee delta (USD) | |
| MRR delta (USD) | |
| Support hours delta | |
| Technical risk / gate | Observe / Assist / Execute / human approval |
| Security / data impact | |

### 4. Out of scope still excluded

List anything adjacent that remains excluded.

### 5. Acceptance for this CO

- [ ] Deliverable definition
- [ ] Test / evidence required
- [ ] Client approver
- [ ] AION commercial approver
- [ ] Runtime/Security consulted if new governed write or connector

### 6. Authorization

| Role | Name | Decision | Timestamp (America/New_York) |
|---|---|---|---|
| Client | | APPROVE / DENY | |
| AION Commercial | | APPROVE / DENY | |
| AION Delivery | | ACK | |
| Security (if required) | | APPROVE / N/A | |

Work starts only after required APPROVE rows are filled. Superseding COs get a
new id; do not silently edit a signed CO.
