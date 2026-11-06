# ADR-020: `aion-security` Repository Ownership

- **Status:** Accepted
- **Date:** 2026-11-06
- **Decision Owners:** Architect (accepted); TechOps / Security Infrastructure
- **Related:** [ADR-001](ADR-001-greenfield-reset.md),
  [ADR-004](ADR-004-aion-desks-repo-ownership.md),
  [ADR-005](ADR-005-runtime-identity-plane.md),
  [ADR-007](ADR-007-observe-assist-execute-tiers.md),
  [ADR-012](ADR-012-secure-execution-layer-v1.md),
  [`../repositories/aion-security.md`](../repositories/aion-security.md),
  [`../architecture/security-infrastructure-standard-v1.md`](../architecture/security-infrastructure-standard-v1.md),
  [`../architecture/continuous-assurance-v1.md`](../architecture/continuous-assurance-v1.md),
  [`../offers/techops-security-operations-lite.md`](../offers/techops-security-operations-lite.md)

## Context

AION’s security posture is already specified across:

- **Standards** in `aion-docs` (SIS, Secure Execution Layer, Continuous Assurance)
- **Enforcement** in `aion-core` (PolicyEngine, gates) and `aion-runtime` (Gateway,
  identity plane, `/v1/assurance/*` prototype)
- **Host hardening** in `aion-infra` (secrets, networking, digest-pinned deploys)

TechOps commercial packaging explicitly treats **penetration testing / red team**
as a separate engagement from Continuous Assurance
([techops-security-operations-lite.md](../offers/techops-security-operations-lite.md)).

Without a recorded ownership decision, red/blue agent code risks landing in:

1. `aion-runtime` (turning the composition root into a SIEM / attack harness),
2. `aion-infra` (mixing IaC with agent playbooks), or
3. a seventh “canonical” platform plane that dilutes ADR-001’s six-repo model.

`https://github.com/Ceoloo/aion-security` was created to hold that operational
surface. It needs the same satellite clarity ADR-004 gave `aion-desks`.

## Decision

**We keep `aion-security` as a permanent TechOps security satellite repository
outside the six ADR-backed platform repositories.**

Concretely:

1. **Platform canon remains six repos.** No seventh platform plane.
2. **`aion-security` owns** blue Continuous Assurance connectors/runners, red
   adversarial playbooks (default Observe; process-gated), security agent
   manifests (intent), and TechOps evidence packaging helpers.
3. **`aion-security` does not own** PolicyEngine, ApprovalGate, Execution
   Gateway, canonical Postgres schema, SIS/CA/SEL normative text, or platform
   IaC / secrets architecture.
4. **Dependency rule:** `aion-security` may call Runtime over HTTPS with bearer
   + tenant (ADR-005 / ADR-007 fail-closed). It must **not** import `@aion/core`
   or `@aion/data` as a parallel OS. Remediations that mutate systems must go
   through `POST /v1/commands` when implemented.
5. **Red team defaults:** disabled unless `AION_RED_ENABLED` is set; production
   requires an explicit playbook allow-list; foundation manifests must not
   declare Execute tier for red agents.
6. **Docs:** [`repositories/aion-security.md`](../repositories/aion-security.md)
   under workspace satellites; maps cite this Accepted ADR.

## Alternatives Considered

- **Alternative A — Promote to seventh canonical repository**  
  **Rejected:** security ops are delivery/operations code consuming the
  platform, not a new control/data/runtime plane. Inflating the canon weakens
  the six-plane model.

- **Alternative B — Fold into `aion-runtime`**  
  **Rejected:** Runtime already hosts gateway + assurance prototype. Growing
  connector sprawl and red harnesses there conflates composition root with
  TechOps delivery and raises blast radius.

- **Alternative C — Fold into `aion-infra`**  
  **Rejected:** infra owns host/env/IaC. Agent playbooks and assurance
  connectors are application-level operations, not Terraform/Compose.

- **Alternative D — Leave undocumented / chat-only**  
  **Rejected:** ownership must not live only in chat (same failure mode ADR-004
  closed for desks).

## Consequences

### Positive

- Clear home for red/blue agents without forking the control plane.
- Aligns with TechOps ladder (Foundation / Secure AI Execution / Continuous
  Assurance) and the “red team as separate engagement” SKU boundary.
- Preserves Gateway hard rule: `Agent → Runtime → Policy → Adapter`.

### Negative

- Another workspace git root (cognitive overhead).
- Risk of dual assurance paths (local connectors vs Runtime `/v1/assurance`)
  if not documented — mitigated by treating Runtime as platform authority and
  this repo as delivery connectors.
- Red harness misuse risk — mitigated by default-deny flags and allow-lists.

## Implementation Notes

| Artifact | Action |
|---|---|
| This ADR | Status → **Accepted** |
| [`repositories/README.md`](../repositories/README.md) | Satellite table cites ADR-020 |
| [`repositories/aion-security.md`](../repositories/aion-security.md) | Ownership page |
| `aion-security` README | Points at ADR-020 |
| Runtime `/v1/assurance` | Remains platform control surface |
| Future remediations | Mission-gated; submit via Gateway |

## Follow-up Decisions

- Whether durable assurance evidence tables graduate into `aion-data` (only when
  multi-product reuse justifies platformization).
- Whether Operator Console gains a first-class Assurance view that consumes
  Runtime APIs (product UI — not this ADR).
- Whether any red Execute-tier playbook is ever allowed (requires a dedicated
  mission + risk review; not implied by this ADR).
