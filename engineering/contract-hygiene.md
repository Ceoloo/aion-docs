# Contract Hygiene

Cross-repo composability depends on **one owner per contract** and **pinned,
rebuildable dependencies**. This note turns the architecture audit into
enforceable engineering practice.

Related ADRs: [ADR-005](../adr/ADR-005-package-dependency-direction.md),
[ADR-006](../adr/ADR-006-postgres-durable-store.md),
[ADR-007](../adr/ADR-007-products-runtime-http-client.md),
[ADR-008](../adr/ADR-008-runtime-composition-scope.md).

## 1. Extend Core ports (stop DataLayer bypass)

**Problem:** Runtime often reads/writes durable concerns through `DataLayer`
fields that have no Core port. Core cannot swap/mock those paths; the ports
story is incomplete.

**Rule:** If Runtime needs durable state for a governed path, define or extend a
port in `aion-core`, implement it in `aion-data`, and consume the port from
Runtime.

**Initial backlog (non-exhaustive):** executions, services/catalog, outcomes,
economics rollups, autonomy grants, external side effects, revenue sessions /
implementation cases (if retained as platform).

## 2. One Runtime client + DTO surface in products

**Problem:** `aion-products` mirrors Runtime/Data types in more than one module
(`runtime-contracts`, workforce console types, duplicate HTTP helpers). Silent
drift is likely.

**Rule:**

- Single HTTP client module for server-side product code.
- Single DTO module (or generated types) for Gateway payloads.
- Workforce/UI should import shared types or OpenAPI artifacts — not a second
  hand-written fork.

Prefer publishing a versioned contracts artifact later; until then, one in-repo
module is mandatory.

## 3. Pin packages to tagged releases

**Problem:** `file:vendor/...` clones at drifting SHAs, with Runtime overlays
mutating vendored Data trees, break “single source of truth.”

**Rule:**

- Pin `@aion/core` and `@aion/data` to **git tags / released versions**.
- Forbid post-clone tree surgery overlays.
- When a feature needs Data+Core changes, land them on main with a tag, then
  bump consumers.

## 4. Events and HTTP as contracts

- Control-plane lifecycle events remain owned by Core (`event-standards.md`).
- Gateway HTTP paths are versioned under `/v1` and documented beside Runtime;
  breaking changes require an ADR or explicit version bump.
- Domain business events (e.g. CRM facts) must not be smuggled as command
  payloads without an owned schema.

## 5. Review checklist (PR)

- [ ] New durable field has a migration owner in `aion-data` and a contract/port
      owner in `aion-core` (unless product-opaque blob explicitly allowed).
- [ ] Products did not add `@aion/runtime` package dependency.
- [ ] Staging/production composition requires `AION_RUNTIME_URL`.
- [ ] Runtime changes do not grow gateway god-files without extraction plan
      reference ([runtime-slim-down](../architecture/runtime-slim-down.md)).
- [ ] Dependency pins are tags, not ad-hoc overlays.
