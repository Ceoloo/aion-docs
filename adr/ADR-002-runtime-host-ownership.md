# ADR-002: Runtime Host Ownership — the `aion-runtime` Repository

- **Status:** Accepted
- **Date:** 2026-09-01
- **Decision Owners:** Ceoloo (Founder / CEO)

## Context

AION Core is a control-plane **kernel/library** and AION Data is a
**persistence package**; neither is a long-running service. To operate the
platform, something must be a **composition root**: a thin process that wires a
real Core `Orchestrator` to AION Data's adapters, connects to PostgreSQL,
exposes health/readiness, emits structured logs, handles graceful shutdown, and
runs migrations — the runtime side of the
[aion-infra deployment contract](https://github.com/Ceoloo/aion-infra/blob/main/contracts/deployment-contract.md).

Phase 3 (aion-infra) built this composition root as an in-repo **reference
fixture** (`aion-infra/runtime/`) and flagged that it had no correct permanent
home, because it creates a boundary tension none of the existing five
repositories can absorb:

- The [dependency rules](../repositories/dependency-rules.md) forbid
  `aion-infra` **code** from importing `aion-core`/`aion-data` — infra "runs on"
  the platform, it does not import it. So the host cannot permanently live in
  infra.
- `aion-core` is deliberately **database-agnostic** — it must not import a
  Postgres layer. The host imports `@aion/data` (Postgres), so it cannot live in
  core without violating that invariant.
- `aion-data` is **orchestration-agnostic** — the host wires an orchestrator, so
  it cannot live in data.
- The host is **not a product**; putting the platform runtime in `aion-products`
  would blur the platform/product seam (a *product* may later have its own
  composition root — that is a different concern).

The runtime host therefore needs an owner before the first product (Phase 4)
attaches to the platform. This was recorded as an accepted cross-repo decision
in aion-infra (`docs/adr/ADR-0001-runtime-host-ownership.md`) with an explicit
instruction to ratify it here; this ADR is that ratification.

## Decision

**We will create a sixth canonical repository, `aion-runtime`, as the owner of
the AION runtime host (the platform composition root).** AION now has **six**
canonical repositories, not five.

- `aion-runtime` depends on `@aion/core` and `@aion/data` — both **downward**
  dependencies, permitted by the dependency rules (the same direction
  `aion-products` uses).
- It owns the runtime side of the deployment contract: config validation,
  the Core⇄Data wiring, HTTP liveness/readiness, structured logging, graceful
  shutdown, the migration entrypoint, and the release-metadata surface. It
  builds the single, **provider-neutral container image** every deployment
  profile runs.
- `aion-infra` consumes `aion-runtime` **only as a built image**, never as a
  code dependency. Infra keeps owning provisioning, provider profiles, secret
  injection, networking, and deployment mechanics.
- `aion-runtime` imports **no** cloud/provider SDK; provider specifics stay in
  aion-infra's provider profiles.

## Alternatives Considered

- **`aion-core/runtime` subpackage** — Rejected: Core must stay
  database-agnostic; the host imports `@aion/data` (Postgres), which Core must
  never do.
- **`aion-products` composition root** — Rejected: the platform runtime is not a
  product; this blurs the platform/product boundary.
- **Keep it permanently in `aion-infra/runtime/`** — Rejected: violates the
  dependency rules (infra code would import core/data). Acceptable only as the
  interim Phase 3 fixture, which this decision retires.
- **A dedicated `aion-runtime` repository** — Accepted: depends on core+data
  downward (allowed); single accountable owner; infra consumes only the image;
  every purity invariant holds.

## Consequences

### Positive

- Every dependency invariant holds simultaneously: core stays DB-agnostic, data
  stays orchestration-agnostic, infra imports neither, and the runtime has one
  accountable owner.
- The deployment contract is unchanged — it is already host- and
  provider-neutral, so `aion-runtime` slots behind it and every provider profile
  (VPS/AWS/GCP) keeps working with no change.
- Portability is preserved: `aion-runtime` produces the one image all profiles
  deploy (aion-infra's portability invariant, unaffected).

### Negative

- A sixth canonical repository enters the system — a small, real increase in
  surface area. It is justified now (the platform cannot be operated without a
  composition root, and no existing repo may hold it), consistent with
  [Mission Before Infrastructure](../engineering/principles.md): this is a
  current platform requirement, not speculation.
- A one-time extraction of `aion-infra/runtime/` into `aion-runtime`, and a
  build-order insertion (below).

## Implementation Notes

- **Affected docs (this change):** [repositories/README.md](../repositories/README.md)
  (five→six), [repositories/aion-runtime.md](../repositories/aion-runtime.md)
  (new per-repo detail), [repositories/dependency-rules.md](../repositories/dependency-rules.md)
  (new row/column + the "deploys image" edge), [roadmap/build-order.md](../roadmap/build-order.md)
  (a Runtime unit at the Phase 3→4 boundary), and the root
  [README](../README.md).
- **Dependency direction:** `aion-runtime → aion-core` (yes),
  `aion-runtime → aion-data` (yes), `aion-runtime` runs on `aion-infra`
  (runtime only). `aion-infra → aion-runtime` is **image consumption**, not a
  code dependency; no cycle is introduced.
- **Extraction:** move `aion-infra/runtime/**` (config, logger, control-plane,
  server, smoke, index, migrate, `sql/grants.sql`, `Dockerfile`, `setup-deps`,
  `package.json`, `tsconfig`) into `aion-runtime`; aion-infra deletes its
  fixture and points every provider profile + CI at the published
  `aion-runtime` image. aion-data migrations remain authoritative; the migrate
  entrypoint is unchanged.
- **Boundary tests:** the provider-neutrality checks that inspect runtime source
  move to `aion-runtime`; aion-infra keeps the cross-profile checks (same image
  var, same migrations, provider tech confined to profiles).

## Follow-up Decisions

- Whether a Phase 4 product runs **inside** `aion-runtime` or brings its own
  composition root — deferred to the first product mission.
- Pinning `aion-runtime`'s `@aion/core`/`@aion/data` dependencies to **tagged
  releases** once those repositories cut tags (currently vendored by pinned
  commit).
