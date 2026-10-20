# Sovereign Model Router

> **Status:** Binding contract (ADR-017)  
> **Doctrine:** [sovereign-intelligence-doctrine.md](sovereign-intelligence-doctrine.md)  
> **Mission:** [SOV-002](../.aion/missions/SOV-002-local-inference-model-router.md)  
> **Related:** [intelligence-layer.md](intelligence-layer.md), [ADR-018](../adr/ADR-018-restricted-external-gateway.md)

The router decides **where inference runs**. It does not embed vendor SDKs.
Capabilities are named by intent (`summarize`, `classify`, `reason`); backends
are swappable.

---

## 1. Inference classes

| Class | Definition | Allowed backends |
|---|---|---|
| `proprietary` | Any prompt/context that includes company IP, customer records, internal workflows, confidential knowledge, operational memory, or non-public product/architecture detail | Local / AION-controlled only |
| `non_proprietary` | Explicitly scrubbed, synthetic, public, or policy-cleared content with **no** protected fields | Local **or** public API via Restricted External Gateway |
| `unknown` | Classification missing or inconclusive | **Fail closed** — treat as proprietary; deny public egress |

**Default for product Copilot / Revenue / agent memory paths:** `proprietary`.

---

## 2. Routing algorithm

```text
classify(request) → inferenceClass
if inferenceClass == unknown:
  deny_public; require_reclassify_or_treat_as_proprietary
if inferenceClass == proprietary:
  try local_runtime
  else try aion_private_endpoint
  else try deterministic_fallback (if capability allows)
  else FAIL_CLOSED  # no silent frontier fallback
if inferenceClass == non_proprietary:
  if policy.allows(public_provider, capability):
    may use public API via REG
  else:
    use local
emit RouteDecision telemetry (class, backend_kind, deny_reason?)
```

### Backend kinds (telemetry)

| `backendKind` | Meaning |
|---|---|
| `local` | Process or host under AION keys (ollama/vLLM/llama.cpp/etc.) |
| `aion_private` | AION-operated endpoint inside trust boundary |
| `deterministic` | Non-model path |
| `public_frontier` | Anthropic / OpenRouter / OpenAI / xAI / … — **only** if `non_proprietary` + REG allow |
| `denied` | Policy deny |

---

## 3. Core contract sketch (`model.generate@1`)

Types land in `aion-core` (no SDK imports). Products/Runtime implement adapters.

```ts
// Conceptual — implement in aion-core; do not copy vendor types.

type InferenceClass = 'proprietary' | 'non_proprietary' | 'unknown';
type BackendKind =
  | 'local'
  | 'aion_private'
  | 'deterministic'
  | 'public_frontier'
  | 'denied';

interface ModelGenerateRequest {
  capability: string;          // e.g. "reason.over_context"
  system: string;
  user: string;
  inferenceClass: InferenceClass;
  maxTokens: number;
  // model id is a hint for local catalog — never a required public vendor pin
  preferredModel?: string;
}

interface ModelRouteDecision {
  inferenceClass: InferenceClass;
  backendKind: BackendKind;
  providerName: string;        // llm.name — e.g. "local", "openrouter"
  model: string;
  denyReason?: string;
}

interface ModelGenerateResult {
  text: string;
  route: ModelRouteDecision;
  tokensIn: number | null;
  tokensOut: number | null;
  latencyMs: number;
  ok: boolean;
  errorCode?: string | null;
}
```

Align with existing products `LlmProvider` / `safeGenerate` telemetry
(RES-001): `ok`, `errorCode` redacts secrets, no stream/tool_call required for
v1.

---

## 4. Classification rules (v1)

Mark **`proprietary`** if any of the following are present in system/user/
retrieved context:

- Customer / prospect PII or CRM payloads
- Call transcripts, objections, deal notes
- Internal runbooks, architecture, source, pricing strategy
- Tenant operational memory / lessons tied to real customers
- Credentials, tokens, or infrastructure details

Mark **`non_proprietary`** only when **all** hold:

- Explicit caller declaration `inferenceClass: 'non_proprietary'`
- Content scrubbed or synthetic (documented scrub profile)
- No retrieved proprietary memory attached
- Capability allowlisted for public backends in REG policy

Otherwise → `unknown` → fail closed.

---

## 5. Local inference Phase 2 expectations

| Requirement | Bar |
|---|---|
| License | Suitably licensed open-weight weights AION may run and serve |
| Host | AION-controlled VPS/GPU/CPU; keys and logs under AION custody |
| Network | Proprietary path does not require public internet to the model vendor |
| Eval | Keyless CI fakes; staging eval pack compares local vs prior frontier quality on scrubbed fixtures |
| Cutover | Products default proprietary → local; frontier path removed or REG-gated `non_proprietary` only |

Technology choice (ollama vs vLLM vs llama.cpp, model family) is deferred to an
implementation ADR once SOV-002 spike proves a working path.

---

## 6. Temporary exceptions

A proprietary workflow may keep frontier **only** with:

1. A time-bounded **exception ADR** naming the workflow, expiry, and scrubbing;
2. Human gate on enablement;
3. Telemetry flag `exceptionAdr=<id>`;
4. Removal ticket before expiry.

Silent OpenRouter “fallback when local fails” is **forbidden**.

---

## 7. Ownership

| Concern | Owner |
|---|---|
| Policy + ADR | Architect |
| Core types | AI / Agent Engineer |
| Local host + deploy | Infrastructure Engineer |
| Products cutover | AI + Product Engineer |
| REG allowlists for public backends | Security Engineer (ADR-018) |
| Quality evals | AI + QA |
