# aion-infra

**Role:** The infrastructure foundation. **Infrastructure must support the
architecture rather than dictate it.**

## Owns

- cloud infrastructure
- environments
- deployment patterns
- Terraform / IaC
- networking
- secrets architecture
- runtime infrastructure
- monitoring infrastructure
- security infrastructure
- CI/CD infrastructure where appropriate
- environment isolation

## Does NOT own

Business logic, orchestration decisions, canonical data definitions, or product
logic. `aion-infra` provides the ground the platform runs on; it does not decide
*what* the platform does.

## The rule that matters most

**Mission before infrastructure.** Infrastructure is created because an active
mission, current product requirement, platform requirement, risk-mitigation
requirement, or validated scaling constraint justifies it — never because AION
"may eventually need it." No speculative distributed systems. See
[../engineering/principles.md](../engineering/principles.md).

## Key architectural references

- [../architecture/environments.md](../architecture/environments.md) —
  environment isolation and promotion.
- [../architecture/security-model.md](../architecture/security-model.md) —
  identities, secrets, least privilege.
- [../governance/change-management.md](../governance/change-management.md) —
  governed promotion to production.

## Deferred technology choices

Cloud provider, IaC tooling, broker, and monitoring stack are selected via ADR
when a real requirement exists. Documentation states the required *capability*
(e.g. "isolated, separately-credentialed environments"; "durable asynchronous
event delivery once throughput/reliability justify a broker"), not a vendor.

## Hard rule

**No secrets are ever committed** to this or any repository. Secrets live in a
managed secret system defined by this repo's secrets architecture.

> **Status:** Not yet built. `aion-infra` is **Phase 3** — and only the
> *minimum* infrastructure required by current missions. See the
> [build order](../roadmap/build-order.md).
