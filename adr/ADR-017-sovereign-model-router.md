# ADR-017: Sovereign Model Router

- **Status:** Accepted
- **Date:** 2026-10-20
- **Decision Owners:** Ceoloo (Founder / CEO)
- **Mission:** [SOV-002](../.aion/missions/SOV-002-local-inference-model-router.md)
- **Doctrine:** [ADR-016](ADR-016-sovereign-intelligence-doctrine.md)

## Context

SOV-001 found that **proprietary Copilot / Revenue paths today call public
frontier APIs** (Anthropic direct and/or OpenRouter) with live conversation and
business context. That violates ADR-016 intelligence sovereignty and the
“zero unauthorized IP exposure” rule.

RES-001 already established:

- product-local `LlmProvider` is the right isolation for a single consumer;
- **no vendor SDKs in `aion-core` or `aion-runtime`**;
- a narrow Core `model.generate@1` contract is **GO IN PRINCIPLE** pending ADR;
- adaptive auto-switching is a non-goal until live evidence exists.

AION needs a binding **routing policy** before more products embed frontier
defaults: proprietary work must prefer local / AION-hosted inference; public
APIs remain optional only for classified non-proprietary tasks via the
Restricted External Gateway ([ADR-018](ADR-018-restricted-external-gateway.md)).

## Decision

1. **Adopt a Sovereign Model Router policy** (full contract:
   [../architecture/sovereign-model-router.md](../architecture/sovereign-model-router.md)).

2. **Inference classes (mandatory on every model call):**
   - `proprietary` — company IP, customer/operational context, internal
     workflows, confidential knowledge → **local / AION-controlled inference
     only**. Public frontier APIs are denied.
   - `non_proprietary` — scrubbed, synthetic, or explicitly public content →
     may use public APIs only through Restricted External Gateway + policy
     allow.
   - `unknown` — **fail closed** (treat as proprietary / deny public egress).

3. **Routing order for `proprietary`:**
   1. Local / self-hosted open-weight runtime on AION-controlled hardware
   2. AION-operated private inference endpoint (same trust boundary)
   3. Deterministic / non-model path if quality bar allows
   4. **Never** public Anthropic / OpenRouter / OpenAI / xAI for proprietary

4. **Core contract (types only, no SDKs):** introduce capability-shaped
   `model.generate@1` (or successor) in `aion-core` — request/response +
   `inferenceClass` + `routeDecision` telemetry. Vendor adapters stay in
   products or a future Runtime-hosted **local** adapter package — never
   provider SDKs inside Core.

5. **Products must stop defaulting proprietary work to frontier.** Until a
   local runtime is production-ready, proprietary calls that cannot run locally
   **fail closed** or use an explicitly human-gated temporary exception
   (time-bounded exception ADR) — not silent OpenRouter fallback.

6. **Practical starting point:** suitably licensed open-weight models on
   controlled VPS/GPU (or CPU for smaller models). Training foundation models
   from scratch is **not** required for Phase 2.

## Alternatives Considered

- **Keep OpenRouter as default; add “prefer local” later.** Rejected: continues
  unauthorized IP exposure for every proprietary call.
- **Put LiteLLM / Bedrock proxy in Runtime now.** Rejected: premature infra
  (RES-001); revisit only with multi-product ops evidence. Local runtime first.
- **Train all models in-house before shipping.** Rejected: unnecessary for
  sovereignty; open-weight local inference is the practical start.
- **Adaptive multi-provider auto-routing.** Rejected for now (RES-001); policy
  routing by `inferenceClass` is enough for SOV-002.

## Consequences

### Positive

- Clear deny/allow for frontier vs local; aligns products with ADR-016.
- Unblocks Core types without importing SDKs.
- Model replaceability preserved (capability name, not vendor).

### Negative

- Near-term quality/latency risk until local models are sized and eval’d.
- Temporary exception ADRs may be needed for revenue-critical paths during
  cutover — must be explicit and time-bounded.
- Infra cost for GPU/CPU inference hosts.

## Implementation Notes

| Slice | Owner | Repo |
|---|---|---|
| Architecture contract (this ADR + doc) | Architect | `aion-docs` |
| Core `model.generate@1` types + `inferenceClass` | AI Engineer | `aion-core` |
| Local inference host (ollama / vLLM / llama.cpp — choose in implement ADR) | Infra + AI | `aion-infra` + Runtime or products adapter |
| Products router: classify + deny frontier for proprietary | AI + Product | `aion-products` |
| Evals for local vs frontier quality | AI + QA | `aion-products` / docs |
| REG policy for any remaining public inference | Security | ADR-018 |

Follow RES-001 kill criteria: keyless CI; no secrets in logs; `llm.name` not
raw vendor strings in execution metadata where Core contracts apply.

## Follow-up Decisions

- Concrete local runtime technology choice (capability ADR when selected).
- Whether Runtime hosts a **local-only** inference sidecar vs products calling
  a private endpoint — decide after first working local path.
- Time-bounded exception ADR if a named proprietary workflow must keep frontier
  during cutover (must list expiry + scrubbing requirements).
