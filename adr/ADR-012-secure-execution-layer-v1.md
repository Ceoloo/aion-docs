# ADR-012: Secure Execution Layer v1

- **Status:** Accepted
- **Date:** 2026-10-06
- **Decision Owners:** TechOps / Security Infrastructure Layer
- **Linear:** AIO-45

## Context

AIO-43 published the Security Infrastructure Standard (SIS). AIO-44 landed the
Agent Identity Registry schema, API, and inventory. Sensitive execution already
enters through the Runtime Execution Gateway ([ADR-003](ADR-003-execution-gateway-into-runtime.md)),
but the **gateway/control-plane contract** — required controls, threat model,
and migration from partial product paths — was not yet a single normative
artifact.

Industry signals (Blueprint Alliance, Google Agent Gateway, NIST token guidance)
converge on: authenticated principal → scoped authority → gateway enforcement →
runtime monitoring → rapid containment.

## Decision

1. **Secure Execution Layer (SEL) v1** is the normative contract documented in
   [../architecture/secure-execution-layer-v1.md](../architecture/secure-execution-layer-v1.md).
2. **SEL is not a new microservice.** It is the Execution Gateway in
   `aion-runtime` plus Core policy plus Data audit ledger plus infra secrets —
   conforming to SIS-GX-* controls.
3. **Required controls** (authenticated principal, tenant binding, tool
   allow-lists, Observe/Assist/Execute, data-scope, human approval, vaulted
   credentials, execution evidence, rate/spend, emergency revoke) are mandatory
   for production Execute-tier paths.
4. **Threat model v1** in the SEL spec is the baseline for reviews and AIO-46
   customer-zero scoring of domain I / gateway controls.
5. **Migration** follows M0–M7 in the SEL spec; parallel control planes are
   explicit debt until retired.

## Alternatives considered

| Option | Why rejected |
|---|---|
| New standalone gateway service | Violates ADR-003 / ADR-002 composition-root |
| Prompt-only “policy” | Fails SIS-AG-05 / fail-closed doctrine |
| Defer threat model to AIO-47 | Continuous Assurance needs a fixed control contract first |

## Consequences

- Products and adapters must meet SEL interfaces; gaps score on customer-zero
  audit (AIO-46) and block Execute-ready banding.
- Registry revoke + FeatureGate + autonomy demote are the v1 contain toolkit;
  deeper IdP/vault orchestration remains follow-on.
- AIO-47 Continuous Assurance connectors attest against this contract.

## References

- SIS v1 §3.10 (`SIS-GX-*`)
- ADR-003, ADR-005, ADR-007, ADR-008
- Linear AIO-45; related AIO-23 / AIO-29 (OpenShell containment)
