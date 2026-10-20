# ADR-018: Restricted External Gateway (IP Policy)

- **Status:** Accepted
- **Date:** 2026-10-20
- **Decision Owners:** Ceoloo (Founder / CEO)
- **Mission:** [SOV-003](../.aion/missions/SOV-003-restricted-external-gateway.md)
- **Doctrine:** [ADR-016](ADR-016-sovereign-intelligence-doctrine.md)
- **Builds on:** [ADR-003](ADR-003-execution-gateway-into-runtime.md), [ADR-012](ADR-012-secure-execution-layer-v1.md)

## Context

ADR-016 requires a **Restricted External Gateway**: default deny; only
non-proprietary (or explicitly authorized SoE) tasks may leave AION’s boundary;
every egress carries identity, permissions, observability, and an IP-exposure
check.

Today:

- Runtime already is the Execution Gateway (ADR-003) and hosts **GHL adapters**
  that mutate CRM state.
- Products call **frontier LLM APIs** directly (side path around Runtime) —
  SOV-001 P0 finding.
- Stripe (Desks), B2 backups, ntfy, Vercel, GitHub are separate egress classes
  without a unified IP policy object.
- `ExternalSideEffect` records CRM mutations but does not encode
  `ipExposureClass` or default-deny for inference/public APIs.

Without a policy object, “optional external” remains aspirational.

## Decision

1. **The Restricted External Gateway (REG) is a policy layer on the existing
   Runtime Execution Gateway**, not a second microservice.
   Contract: [../architecture/restricted-external-gateway.md](../architecture/restricted-external-gateway.md).

2. **Default deny** for all outbound calls that leave the AION trust boundary,
   except:
   - allowlisted **SoE / commerce / ops** capabilities with explicit policy
     rows (e.g. GHL CRM mutations, Stripe checkout, B2 backup upload);
   - **`non_proprietary` inference** allowlisted per ADR-017 + REG;
   - localhost / same-host AION services (Postgres, local inference) — not
     “external.”

3. **Every external egress decision emits an auditable record** including:
   `capability`, `provider`, `ipExposureClass`, `policyVersion`,
   `principal`, `tenantId`, `allow|deny`, `denyReason?`.

4. **IP exposure classes:**

   | Class | Meaning | Public egress |
   |---|---|---|
   | `none` | No protected content in payload | May allow if capability allowlisted |
   | `soe_authorized` | CRM/commerce SoE payload; AION retains why/who/outcome | Allow only for named SoE providers (GHL, Stripe, …) |
   | `proprietary` | Company IP / confidential intelligence | **Deny** (except same-boundary local) |
   | `unknown` | Unclassified | **Deny** |

5. **Products must not open raw public API sockets for proprietary work.**
   Frontier inference goes through ADR-017 router; CRM writes stay on Runtime
   adapters; new SaaS adapters are Runtime-only.

6. **Core gains policy types** (no network I/O): `IpExposureClass`,
   `OutboundPolicyRequest`, `OutboundPolicyDecision`. Runtime PolicyEngine (or
   sibling) evaluates before adapter `fetch`.

7. **Initial allowlist (v1)** — subject to Security review on implement:

   | Provider | Capabilities | Class allowed |
   |---|---|---|
   | `ghl` / LeadConnector | Catalogued CRM mutations | `soe_authorized` |
   | `stripe` | Checkout / webhooks (Desks) | `soe_authorized` |
   | `b2` | Encrypted backup upload | `none` (ciphertext) |
   | `ntfy` | Ops alerts (no proprietary bodies) | `none` |
   | `public_frontier` | Inference | `none` only + ADR-017 `non_proprietary` |
   | *(deny all else)* | — | — |

## Alternatives Considered

- **Separate REG microservice.** Rejected: duplicates ADR-003 gateway; more
  drift surface.
- **Trust adapter authors to “be careful.”** Rejected: SOV-001 shows products
  already bypass with direct Anthropic/OpenRouter.
- **Block GHL until fully local CRM.** Rejected: doctrine allows authorized
  externals; GHL remains SoE with ledger — not SoR for protected intelligence.
- **Allowlist only in docs, no code.** Rejected: non-enforceable; fails
  Definition of Done sovereignty gate.

## Consequences

### Positive

- Single place to reason about egress; aligns GHL/Stripe/frontier under one
  model.
- Closes the products→frontier side door when combined with ADR-017.
- Audit spine for sovereignty compliance reviews.

### Negative

- Adapter refactors: every `fetch` to external hosts needs a policy check.
- Desks Stripe path is outside Runtime today — needs either Runtime mediation
  later or a documented **edge allowlist** exception until commerce joins
  gateway.
- False denies during rollout — require staging allowlist dry-runs.

## Implementation Notes

| Slice | Owner | Repo |
|---|---|---|
| Policy contract (this ADR + architecture doc) | Architect + Security | `aion-docs` |
| Core types: IpExposureClass + OutboundPolicy* | Security + Core | `aion-core` |
| Runtime evaluate-before-adapter | Runtime + Security | `aion-runtime` |
| GHL adapter: attach class `soe_authorized` | Integrations | `aion-runtime` |
| Products: remove direct frontier for proprietary (ADR-017) | AI | `aion-products` |
| Extend `ExternalSideEffect` or parallel egress event | Data + Core | `aion-data` / `aion-core` |
| Desks Stripe edge note | Product + Security | exception or later mediation |

## Follow-up Decisions

- Whether Stripe must move behind Runtime (vs lasting edge allowlist).
- Egress event schema vs extending `ExternalSideEffect` for non-CRM providers.
- Kill-switch FeatureGate flag `reg.enforce` (observe → enforce rollout).
