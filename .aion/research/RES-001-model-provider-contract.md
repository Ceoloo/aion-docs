# RES-001 — Model-provider integration contract

**Status:** Research complete (recommendation). Prototype not started.  
**Owner:** AION — RESEARCH  
**Linear:** [AIO-16](https://linear.app/aion-empire/issue/AIO-16/p0-implement-model-provider-integration-contract)  
**Mission overlay:** [../missions/RES-001-model-provider-contract.md](../missions/RES-001-model-provider-contract.md)

---

## Question

What narrow, provider-neutral **model interface + telemetry contract** should
AION adopt so OL-001 can exercise live model I/O without coupling Core/Runtime
to a single vendor — and without promoting product-local adapters prematurely?

## Existing contracts / ADRs inspected

| Source | Finding |
|---|---|
| [Intelligence layer](../../architecture/intelligence-layer.md) | Reasoning is a governed capability; name the *capability*, not a vendor |
| [Platform maturity — platformization rule](../../roadmap/platform-maturity.md) | Promote to Core only when reused, understood, stable, and coupling-positive |
| [ADR-002](../../adr/ADR-002-runtime-host-ownership.md) | Runtime image is provider-neutral; **no** cloud/provider SDKs in Runtime |
| [Mission 007](../../architecture/execution-platform-v1.md) | Scorecards + `/v1/routing/recommend` are **recommendation-only**; adaptive auto-switch is a non-goal |
| [aion-core Phase 1](https://github.com/Ceoloo/aion-core/blob/main/docs/phase-1.md) | Model-provider integrations intentionally deferred |
| [Product OpenRouter design](https://github.com/Ceoloo/aion-products/blob/main/docs/design/openrouter-provider.md) | `LlmProvider` + Anthropic + OpenRouter **already implemented in products** |
| [PRE-OL validation](../../roadmap/pre-ol-validation.md) | Live model proof **not yet**; ~3 ms avg latency = stub path |

**Verdict:** AION already has a *product-local* pattern. The research gap is
whether/how to lift a **platform contract** (AIO-16) without violating
Runtime neutrality and the platformization rule.

---

## Evidence pack

### What works today (products)

- Interface: `LlmProvider` with `complete(LlmRequest) → LlmResponse`
  (`aion-products` `provider-contracts` / `provider-adapter`).
- Implementations: Anthropic (SDK), OpenRouter (plain `fetch`, OpenAI-compatible).
- Selection: `AION_LLM_PROVIDER` pin → else key presence → else `null` (deterministic).
- Telemetry today: `model`, `tokensIn`, `tokensOut`, `metadata.provider`; cost
  when available.
- Safety: missing key → deterministic fallback; CI keyless with fake `fetch`.

### What does **not** exist yet (platform)

AIO-16 asks for: `generate`, `structured_output`, `tool_call`, `stream`,
`usage`, `cost` — plus durable recording of provider/model, tokens, latency,
cost, success/error.

Gaps vs product `LlmProvider`:

| AIO-16 surface | Product today | Gap |
|---|---|---|
| `generate` | `complete` (text) | Naming / shape only |
| `structured_output` | Task-level parse after free text | No first-class contract |
| `tool_call` | Not on `LlmProvider` | Missing |
| `stream` | Not supported | Missing |
| `usage` / `cost` | Partial (tokens; cost opportunistic) | Needs required telemetry fields |
| Latency / success / error | Not standardized on provider result | Missing |
| Control-plane binding | Product adapter only | Not invoked as Runtime capability |

### PRE-OL / OL implication

Command Center “100% success” is **runtime success**, not model success. Until
a live provider path records real latency/tokens/cost on governed executions,
OL-001 cannot honestly claim model-backed operating leverage.

---

## Vendor / library comparison (narrow)

| Option | Pros | Cons | Fit |
|---|---|---|---|
| **Keep product-local only** (status quo) | Zero Core/Runtime risk; already ships | Duplicates if Media/other products need LLMs; AIO-16 stays open; Runtime never sees live model telemetry | Acceptable short-term; fails OL “live model proof” if only products call models off-gateway |
| **OpenAI-compatible HTTP adapter** (OpenRouter-style `fetch`) | No SDK in Runtime; multi-model via one HTTP shape; matches existing OpenRouter impl | Tool/stream/structured vary by upstream; still need capability mapping | **Best spike shape** |
| **Vendor SDKs in Core** (Anthropic/OpenAI/Google packages) | Rich APIs | Violates “no model coupling”; Runtime neutrality; lock-in; ADR required and likely **reject** | **Kill for Core/Runtime** |
| **Gateway proxy (e.g. LiteLLM / Bedrock / Netlify AI Gateway)** | Central keys, one API | Extra moving part; Ops cost; premature infra risk | Research-only until multi-product + ops justify ADR |

---

## Recommendation (L1 — recommend only)

1. **Do not** put vendor SDKs in `aion-core` or `aion-runtime`.
2. **Do** define a **narrow platform contract** in Core (types + zod only) named
   as capabilities, e.g. `model.generate@1`, optionally later
   `model.structured_output@1` / `model.tool_call@1` / `model.stream@1`.
3. **Implement adapters in products** (or a future dedicated adapter package
   that Runtime does **not** import) behind `ExecutionAdapter`, reusing the
   OpenRouter/Anthropic pattern.
4. **Require telemetry** on every model result: `provider`, `model`,
   `latencyMs`, `inputTokens`, `outputTokens`, `costUnits?`, `ok`,
   `errorCode?` — align with evaluation / scorecard candidate identity
   (`provider` + `model`) already used by Mission 007.
5. **Defer** `stream` and `tool_call` from the MVP contract if they block
   OL-001; ship `generate` + usage/cost + structured parse-at-task-layer first.
6. **Do not** enable adaptive auto-routing; keep M007 recommendation-only +
   deterministic ExecutionRegistry fallback until live samples exist.
7. **Prototype next** (only after this brief is accepted): isolated branch in
   `aion-products` expanding `LlmProvider` → AIO-16-shaped result + a
   harness that asserts telemetry without production credentials.

### Promotion criteria (go)

- Contract reviewed by Architect; ADR if Core gains new public types.
- Proof: fake-provider tests green; optional live smoke with env-injected key.
- Runtime remains free of provider SDKs.
- Scorecard path can ingest real provider/model samples from governed runs.

### Kill criteria (no-go)

- Proposal requires Runtime to import a vendor SDK.
- Proposal couples business logic to one model id.
- Proposal enables auto provider switching without eval sample depth.
- Prototype cannot run keyless in CI.

---

## Risks

| Risk | Mitigation |
|---|---|
| Premature platformization of product `LlmProvider` | Keep adapters in products until a second product reuses the same interface |
| Silent production dependency via shared secrets | Spikes use env-only keys; never commit secrets |
| OL blocked waiting for perfect AIO-16 surface | MVP = generate + telemetry; defer stream/tool_call |
| Dual paths (product LLM vs Runtime mock) confuse metrics | Tag `cohort` / `synthetic` / `productionEconomic` honestly; PRE-OL already models this |

---

## Next recommended action

1. Architect reviews this brief (go/no-go on Core contract vs product-only).
2. If go: RESEARCH opens an isolated products spike for telemetry-complete
   `generate` (no stream/tool yet) + handoff to AI / Runtime for capability
   registration.
3. Integrations continues AIO-17 in parallel (orthogonal).
