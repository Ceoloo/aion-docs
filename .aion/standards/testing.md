# Testing Standards (Cursor agents)

Canon: [`../../engineering/testing.md`](../../engineering/testing.md),
[`../../engineering/evals.md`](../../engineering/evals.md).

Role card: [`../agents/09-qa-engineer.md`](../agents/09-qa-engineer.md).  
Latest posture: [`../audits/reliability-audit-2026-09-12.md`](../audits/reliability-audit-2026-09-12.md).

## Principle

Code is not complete because it compiles.
It is complete when **expected behavior is demonstrated**.

## What every owner owes

| Change type | Minimum proof |
|---|---|
| Pure logic / contracts | Unit tests for happy + failure paths |
| Persistence / migrations | Migration applies cleanly; repository tests; restart survival where relevant |
| Runtime / gateway | Typecheck + build; smoke or proof matrix for touched path |
| Product workflow | Deterministic offline path + any live path gated/documented |
| AI behavior | Evals; no silent eval regression |
| Infra / deploy | Health checks; rollback note; no secret in logs |
| UI packages (e.g. desks) | typecheck + lint + CI build at minimum; smoke for payment/checkout paths |

## QA ownership

**AION — QA** owns the cross-repo bar and frequently reviews other agents’
handoffs. QA may reject a handoff that lacks:

- stated acceptance criteria
- commands run + results
- failure-path coverage for risk-bearing changes
- restart / durability proof when claiming durability
- concurrency / idempotency proof when claiming safe parallel or resume-once behavior

## Restart & reliability

Any claim that “execution is durable” requires evidence that state survives
process restart and leaves an auditable record (`execution_id` / run lineage).
