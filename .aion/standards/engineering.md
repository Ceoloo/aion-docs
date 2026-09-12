# Engineering Standards (Cursor agents)

Authoritative detail lives in [`../../engineering/`](../../engineering/).
This file is the **always-on checklist** every specialist follows.

## Before significant changes

1. Inspect the codebase in the owning repo(s).
2. Search for an existing implementation or contract.
3. Understand interfaces and ports (especially Core ↔ Data ↔ Runtime).
4. Identify dependencies and who else must change.
5. Define acceptance criteria and proof before coding deep.

## Binding rules

- **Do not create duplicate architecture.** Extend the existing abstraction.
- Prefer **vertical slices** that prove real functionality end-to-end.
- **Platform capabilities** belong in `aion-core` / `aion-data` / `aion-runtime`.
- **Product-specific behavior** belongs in `aion-products` (or `aion-desks`).
- **External services** connect through adapters with clear contracts — no
  vendor logic leaking through Core.
- Do not weaken authentication, permissions, or tests to “make it work.”
- Never expose secrets to browser code. Never commit credentials.
- Avoid destructive migrations. Prefer additive, reversible schema changes.
- Production-impacting changes require rollback consideration.
- Honor [`../../repositories/dependency-rules.md`](../../repositories/dependency-rules.md).
- Honor the seven principles in [`../../engineering/principles.md`](../../engineering/principles.md).

## Definition of done (summary)

A change is done when it is in the right repo, contracts are updated, tests
(and evals if AI behavior changed) pass, observability is present, governance
holds, no secrets leaked, and docs reflect reality.

Full checklist: [`../../engineering/definition-of-done.md`](../../engineering/definition-of-done.md).

## Mission before infrastructure

Do not build queues, caches, brokers, or multi-cloud complexity unless an
active mission or validated constraint requires it.
