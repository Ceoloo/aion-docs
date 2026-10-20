# Restricted External Gateway (REG)

> **Status:** Binding contract (ADR-018)  
> **Doctrine:** [sovereign-intelligence-doctrine.md](sovereign-intelligence-doctrine.md)  
> **Mission:** [SOV-003](../.aion/missions/SOV-003-restricted-external-gateway.md)  
> **Gateway host:** [ADR-003](../adr/ADR-003-execution-gateway-into-runtime.md)  
> **SEL:** [secure-execution-layer-v1.md](secure-execution-layer-v1.md)  
> **Inference routing:** [sovereign-model-router.md](sovereign-model-router.md)

REG is the **default-deny outbound policy** for anything that leaves AION’s
controlled boundary. It is a policy layer on the Runtime Execution Gateway —
not a second service.

```text
Agent / Product
  → Runtime Execution Gateway (authn, tenant, SEL)
  → OutboundPolicy.evaluate (REG)
       deny → audit + fail closed
       allow → Adapter (GHL, …) / approved public inference
  → ExternalSideEffect / egress audit record
```

---

## 1. What counts as “external”

| Destination | External? | Notes |
|---|---|---|
| Local Postgres (`aion_data`) | No | Owned data layer |
| Local / AION-private inference | No | Same trust boundary (ADR-017) |
| LeadConnector / GHL HTTPS | **Yes** | SoE — allowlist |
| Anthropic / OpenRouter / OpenAI / xAI | **Yes** | Frontier — `non_proprietary` only |
| Stripe API | **Yes** | Commerce SoE |
| Backblaze B2 | **Yes** | Backup ciphertext |
| ntfy.sh | **Yes** | Ops alerts |
| Vercel / GitHub | **Yes** | Delivery / SCM — exception or separate policy |
| Same-host docker DNS to AION services | No | Internal |

---

## 2. IP exposure classes

| `ipExposureClass` | Payload may contain | Public egress |
|---|---|---|
| `none` | No protected content (or ciphertext only) | If capability allowlisted |
| `soe_authorized` | CRM/commerce fields; AION keeps why/who/outcome | Named SoE providers only |
| `proprietary` | Company IP, confidential intelligence, protected memory | **Deny** |
| `unknown` | Unclassified | **Deny** |

Mapping from ADR-017 inference:

| Inference class | Typical REG class for public frontier |
|---|---|
| `proprietary` | `proprietary` → deny |
| `non_proprietary` | `none` → allow if `public_frontier` capability listed |
| `unknown` | `unknown` → deny |

---

## 3. Policy evaluation (v1)

```ts
// Conceptual Core types — implement in aion-core

type IpExposureClass =
  | 'none'
  | 'soe_authorized'
  | 'proprietary'
  | 'unknown';

interface OutboundPolicyRequest {
  capability: string;          // e.g. "crm.opportunity.update"
  provider: string;            // e.g. "ghl", "public_frontier"
  ipExposureClass: IpExposureClass;
  tenantId: string;
  principalId: string;
  serviceKey?: string;
}

interface OutboundPolicyDecision {
  allow: boolean;
  policyVersion: string;
  denyReason?: string;
  // echo for audit
  capability: string;
  provider: string;
  ipExposureClass: IpExposureClass;
}
```

**Rules (ordered):**

1. If destination is internal (Postgres, local inference) → allow (not REG).
2. If `ipExposureClass` ∈ {`proprietary`, `unknown`} → **deny**.
3. If `(provider, capability)` not in allowlist for this `ipExposureClass` → **deny**.
4. Else allow; require adapter to record egress audit / `ExternalSideEffect`.

Feature flag rollout: `reg.enforce=false` → observe mode (decide + log, do not
block); `true` → fail closed. Default production target: enforce on.

---

## 4. v1 allowlist

| Provider | Example capabilities | Allowed classes |
|---|---|---|
| `ghl` | `crm.contact.*`, `crm.opportunity.*`, `crm.note.*`, `crm.task.*`, `crm.message.*`, `crm.calendar.*` | `soe_authorized` |
| `stripe` | `commerce.checkout.create`, `commerce.webhook.receive` | `soe_authorized` |
| `b2` | `backup.object.put` | `none` |
| `ntfy` | `ops.alert.publish` | `none` |
| `public_frontier` | `model.generate` (non-proprietary only) | `none` |

All other `(provider, capability)` pairs: **deny**.

Security may tighten capability keys to match Runtime service catalog entries.

---

## 5. Audit requirements

Every evaluate() call (allow or deny) must leave an observable record:

- `policyVersion`
- `capability`, `provider`, `ipExposureClass`
- `allow` / `denyReason`
- `tenantId`, `principalId` / actor
- correlation to `executionId` when present

Prefer extending the existing event / side-effect spine over a silent log line.

---

## 6. Products side-door closure

| Anti-pattern | Required posture |
|---|---|
| Product → Anthropic/OpenRouter with proprietary context | Forbidden (ADR-017 + REG) |
| Product → GHL with API key | Forbidden (SEL / ADR-003) |
| Product → Stripe | Allowed at edge for Desks until Runtime mediation; treat as documented SoE allowlist |
| Runtime adapter → GHL | Allowed with `soe_authorized` + ExternalSideEffect |

---

## 7. Threat model additions (sovereignty)

| Threat | Mitigation |
|---|---|
| T-IP1 Prompt exfil via frontier API | ADR-017 classify + REG deny `proprietary` |
| T-IP2 CRM used as intelligence SoR | Policy + docs: GHL is SoE; canonical intelligence in `aion-data` |
| T-IP3 Adapter forgets policy check | Central evaluate-before-fetch; CI grep for raw provider hosts outside allowlist modules |
| T-IP4 Observe-mode forever | Production readiness requires `reg.enforce=true` for critical paths |
| T-IP5 Exception sprawl | Time-bounded exception ADRs only |

---

## 8. Ownership

| Concern | Owner |
|---|---|
| Policy text + allowlist changes | Security Engineer (+ Architect for ADR) |
| Core types | Security + Core |
| Runtime enforcement | Runtime Engineer |
| GHL / future SaaS adapters | Integrations Engineer |
| Inference public path | AI Engineer (ADR-017) |
| Audit schema | Data Engineer |
