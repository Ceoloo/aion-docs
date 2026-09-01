# aion-runtime

**Role:** The **runtime host** — the platform composition root that boots the
control plane over durable data and makes it operable. It is the one process
that turns AION Core (a kernel) and AION Data (a package) into a running,
health-checked, observable service.

Established by [ADR-002](../adr/ADR-002-runtime-host-ownership.md).

## Owns

- the composition root that wires a Core `Orchestrator` to AION Data's adapters
- runtime configuration validation (fail-fast, environment-driven)
- HTTP liveness/readiness endpoints
- structured operational logging (stdout/stderr; no provider SDK)
- graceful shutdown
- the migration execution entrypoint (runs aion-data's authoritative runner)
- the single, **provider-neutral container image** every deployment profile runs

## Does NOT own

Orchestration policy or agent/tool logic (that is `aion-core`), canonical schema
or migrations *content* (that is `aion-data`), provisioning / environments /
provider profiles (that is `aion-infra`), and product logic (that is
`aion-products`). It **composes** the platform; it does not redefine any part of
it.

## Dependencies

- Depends **downward** on `aion-core` and `aion-data` (permitted — same
  direction as products). See [dependency-rules.md](dependency-rules.md).
- **Runs on** `aion-infra` (runtime only — it does not import infra).
- Imports **no** cloud/provider SDK; provider specifics live only in
  `aion-infra`'s provider profiles.
- `aion-infra` consumes `aion-runtime` **as a built image**, not as code — this
  is deployment, not a code dependency, so no cycle is created.

## The rule that matters most

**One image, every provider.** `aion-runtime` produces a single immutable,
provider-neutral image. The VPS, AWS, and GCP profiles in `aion-infra` all
deploy *that* image and inject configuration/secrets through the environment.
The runtime never learns which provider it runs on.

## Key architectural references

- [../architecture/control-plane.md](../architecture/control-plane.md) — the
  orchestrator it hosts.
- [../architecture/data-layer.md](../architecture/data-layer.md) — the durable
  store it connects to via Core's ports.
- [../architecture/observability.md](../architecture/observability.md) — the
  spine its logs and health surface emit against.
- aion-infra `contracts/deployment-contract.md` — the provider-neutral contract
  this repo's image satisfies.

## Hard rule

**No secrets are ever committed.** Secrets are injected as environment values by
the deployment profile; the runtime reads them from the environment and never
from a cloud SDK.

> **Status:** Established by ADR-002 (Phase 3→4 boundary). Seeded by extracting
> the Phase 3 reference host from `aion-infra/runtime/`. See the
> [build order](../roadmap/build-order.md).
