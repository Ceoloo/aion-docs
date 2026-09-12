# ADR-007: Products → Runtime HTTP Client Boundary

- **Status:** Accepted
- **Date:** 2026-09-12
- **Decision Owners:** Architect
- **Related:** [ADR-003](ADR-003-execution-gateway-into-runtime.md),
  [ADR-005](ADR-005-package-dependency-direction.md),
  [`../repositories/dependency-rules.md`](../repositories/dependency-rules.md)

## Context

ADR-003 requires products and external workers to be **clients of Runtime**, not
owners of parallel orchestration state. `dependency-rules.md` forbids a
**package** dependency from products → runtime (avoids cycles and keeps Runtime
a deployable host, not a library).

`aion-products` already talks to Runtime over HTTP (`RuntimeClient`,
`AION_RUNTIME_URL`) while retaining an in-memory `@aion/core` control plane when
the URL is unset. That dual path is useful for tests/demos and dangerous in
production: a misconfigured deploy silently loses durability and governance
ledger writes.

## Decision

1. **Products consume Runtime via HTTP only** — no `@aion/runtime` package
   import. The Execution Gateway is the durable façade for governed commands,
   outcomes, and sessions.
2. **Package deps from products:** `@aion/core` is allowed; `@aion/data` is
   optional and discouraged for production I/O (prefer Runtime).
3. **Fail-closed in durable environments:** when `AION_ENVIRONMENT` is
   `production` or `staging` (or `prod`), `AION_RUNTIME_URL` is **required**.
   Missing URL is a boot/composition error — not a silent fall-back to
   in-memory Core. Packaging-only gates may set
   `AION_ALLOW_IN_MEMORY_CONTROL_PLANE=1` to certify images without a live
   Runtime; real staging/production deploys must not.
4. **In-memory Core remains allowed** for local, test, and explicitly marked
   demo contexts only.

## Alternatives Considered

- **Alternative A — Products import `@aion/runtime` as a library**  
  **Rejected:** collapses composition root into product process; violates
  ADR-002 image boundary.

- **Alternative B — Keep silent in-memory fall-back in production**  
  **Rejected:** recreates the dual control-plane failure ADR-003 called out.

- **Alternative C — Products speak only to `@aion/data` / Postgres**  
  **Rejected:** bypasses policy, catalog, approvals, and side-effect ledger.

## Consequences

### Positive

- Production path matches ADR-003.
- Clear env contract for infra (`AION_RUNTIME_URL` mandatory in staging/prod).

### Negative

- Local DX must set Runtime or opt into local mode deliberately.
- HTTP DTO drift risk until contract hygiene lands shared types.

## Implementation Notes

- Enforce in `aion-products` composition (`AiExecutionService` / server boot).
- Document env vars in product and infra runbooks.
- Consolidate duplicated Runtime client/DTO modules inside `aion-products`
  (see [contract-hygiene](../engineering/contract-hygiene.md)).

## Follow-up Decisions

- Published shared client/types package vs OpenAPI-generated types.
- Whether preview/ephemeral environments count as “durable” (default: yes if
  customer data can flow).
