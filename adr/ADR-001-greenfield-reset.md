# ADR-001: Greenfield Reset

- **Status:** Accepted
- **Date:** 2026-08-31
- **Decision Owners:** Ceoloo (Founder / CEO)

## Context

Earlier AION work — inside the **Aion-Sys** GitHub organization — produced
genuinely valuable material: experiments, documentation, workflows, schemas, and
infrastructure. That work established domain understanding and surfaced real
requirements.

However, architectural ambiguity accumulated over time through:

- **premature infrastructure** — systems built before a validated need;
- **overlapping system responsibilities** — the same concern implemented in
  multiple places;
- **unclear control-plane boundaries** — deciding and doing blurred together;
- **duplicated concepts** — multiple sources of truth for the same entity;
- **runtime-specific assumptions** — coupling to particular models/agent
  runtimes;
- **product logic mixed with platform logic** — no clean seam between the two;
- **systems created before validated demand.**

The cumulative effect is that the existing architecture cannot cleanly answer
the questions a platform must answer: where does new code belong, who owns each
entity, what is allowed, and how do decisions get made. Continuing to build on
it would compound the ambiguity rather than resolve it.

## Decision

**We will leave the historical repositories unchanged in Aion-Sys and build a
fresh canonical architecture** under the personal `Ceoloo` GitHub account, in
five repositories:

- `Ceoloo/aion-docs` — architectural control plane (this repository)
- `Ceoloo/aion-core` — orchestration, agents, tools, permissions
- `Ceoloo/aion-data` — canonical data, events, memory, learning records
- `Ceoloo/aion-infra` — infrastructure and environments
- `Ceoloo/aion-products` — products built on the platform

Legacy Aion-Sys systems **may inform** design decisions but are **not
dependencies** of the new platform. Nothing is migrated, copied, imported, or
depended upon by default. When a concept is carried forward, it is **deliberately
redesigned** or recorded as an explicit carry-forward decision (a future ADR).
See [../legacy/README.md](../legacy/README.md).

`aion-docs` is established **first** (Phase 0) as the architectural and
governance source of truth, before any production implementation begins. See
[../roadmap/build-order.md](../roadmap/build-order.md).

## Alternatives Considered

- **Refactor Aion-Sys in place.** Rejected: the ambiguity is architectural, not
  cosmetic. Refactoring in place preserves the blurred boundaries and duplicated
  sources of truth that caused the problem, and carries their coupling forward.
- **Migrate/copy selected repositories into the new org.** Rejected: wholesale
  copying imports the same overlapping responsibilities and runtime assumptions.
  Carry-forward must be deliberate and per-concept, not bulk.
- **Start coding the new platform without a documented architecture first.**
  Rejected: the original failure mode was building before boundaries and
  ownership were clear. Establishing `aion-docs` first is the direct
  counter-measure.

## Consequences

### Positive

- **Clean boundaries from day one** — explicit ownership, a real control-plane
  seam, platform/product separation.
- **No inherited coupling** — no runtime-specific or premature-infrastructure
  assumptions carried forward silently.
- **Deliberate reuse** — valuable prior work can still be brought forward, but
  only through conscious redesign, improving its quality.
- **A single source of truth for how the system is built** — this repository —
  which is what another engineering agent needs to contribute correctly.

### Negative

- **Re-implementation cost** — useful working code in Aion-Sys is not reused for
  free; it must be redesigned to be brought forward.
- **Temporary capability gap** — the new platform starts at L0 (documented) with
  no running systems.
- **Discipline required** — the reset only pays off if the boundaries and
  principles documented here are actually enforced; drift would recreate the
  original problem.
- **Two worlds during transition** — Aion-Sys remains as reference; care is
  needed not to let it leak in as an implicit dependency.

## Implementation Notes

- This ADR governs the whole ecosystem; it is the founding decision recorded in
  `aion-docs`.
- Repository boundaries and allowed dependencies:
  [../repositories/dependency-rules.md](../repositories/dependency-rules.md).
- Legacy policy: [../legacy/README.md](../legacy/README.md).
- Build order and phases: [../roadmap/build-order.md](../roadmap/build-order.md).
- No secrets, credentials, or code are copied from Aion-Sys into the new
  repositories.

## Follow-up Decisions

Deferred to future ADRs, to be made only when a mission requires them:

- Selection of the event delivery mechanism / broker (capability documented; see
  [../architecture/data-layer.md](../architecture/data-layer.md)).
- Selection of storage engines and the analytics store (`aion-data`).
- Selection of cloud provider, IaC tooling, and environment topology
  (`aion-infra`).
- Standardization of a model/runtime routing policy for execution, if/when needed
  (`aion-core`).
- Any explicit **carry-forward** of a specific concept from Aion-Sys.
