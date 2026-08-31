# API Standards

APIs are contracts between owners and consumers. In AION they are how the
[dependency rules](../repositories/dependency-rules.md) are honored in practice:
a consumer depends on an owner's **API**, never on its internals.

## Principles

- **Every API has a single canonical owner.** The owner defines it and is
  accountable for it. See [../governance/authority-model.md](../governance/authority-model.md).
- **APIs are explicit and versioned.** A consumer can depend on a stable shape;
  breaking changes are versioned, not silent.
- **APIs are least-privilege.** An endpoint exposes only what a consumer needs
  and enforces [permissions](../governance/permissions.md).
- **APIs are observable.** Calls emit the traceability spine. See
  [observability-standards.md](observability-standards.md).
- **APIs respect boundaries.** Products call the platform through APIs; they do
  not reach around them. Core exposes orchestration/tool APIs; data exposes
  read/query and owned-write APIs.

## Design requirements

1. **Explicit contract.** Request and response shapes, error shapes, and
   semantics are defined — not implied by an implementation.
2. **Backward compatibility.** Additive changes only within a version; breaking
   changes require a new version and a migration path. Contract changes that are
   architecturally significant are recorded as an [ADR](../adr/README.md).
3. **Errors are structured and honest.** Failures return actionable, typed
   errors — not opaque 500s — and are observable.
4. **Idempotency where it matters.** Write operations that may be retried are
   designed to be safe to retry.
5. **Risk-aware.** An API that triggers a high-risk action enforces the
   [risk](../governance/risk-levels.md) and [gate](../governance/human-gates.md)
   model; it does not let a caller bypass governance.

## Style is deferred, contracts are not

Whether an interface is REST, RPC, GraphQL, or an internal package boundary is a
per-owner implementation choice (recorded via ADR if it needs standardizing
across repos). What is **not** optional is that the contract is explicit,
owned, versioned, permissioned, and observable.

## Invariants

- **One owner, explicit contract, versioned compatibility.**
- **Least privilege and permission-enforced.**
- **Governance cannot be bypassed through an API.**
- **Observable, with structured errors.**
