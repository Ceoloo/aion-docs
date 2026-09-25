# ADR-011: Adopt UHP / HarnessRouter as the Harness-Execution Seam

- **Status:** Accepted
- **Date:** 2026-09-25
- **Decision Owners:** Founder / CEO; Product Engineer

## Context

AION runs a lot of **harness execution routing**: dispatching a unit of work to
whichever execution environment actually runs an agent loop with a real
workspace — filesystem, shell, git — such as Codex, Claude Code, or a custom
in-house harness. Today that routing is bespoke per harness in the runtime /
infra layer.

[HarnessRouter](https://github.com/HarnessRouter/harnessrouter) (Apache-2.0)
offers a **unified interface for agent harnesses**: one OpenAI Responses-
compatible API (`/v1/responses`, with `metadata.harness_id` selecting the
harness and `model` selecting the model), plus files, sessions, streaming, and
cancellation. Its contract is the **Unified Harness Protocol (UHP)** — versioned,
with machine-readable schemas and a conformance suite. It self-hosts (Docker:
Console / Gateway / Runner), keeping provider keys, sessions, files, and
workspaces local. Self-hosting keeps *state* local, but it does **not** make
model calls the only outbound path: the Runner executes arbitrary shell/tools
and installed harness CLIs, any of which can make non-model network requests
(and could exfiltrate workspace or resolved-secret data). Runner egress is a
policy concern, not a property of self-hosting — see the boundary rules.

This is an **execution-plane** abstraction, not a model router (that is
OpenRouter/LiteLLM) and not a policy layer. The tension is the same one ADR-008
and ADR-010 already resolved for other vendors: AION's thesis is that **authority
is centralized and non-probabilistic**, so a third-party router must not sit
*above* the control plane. The value is real (harness portability, cost/latency
optimization, managed workspaces) but the integration must be bounded.

## Decision

1. **We adopt UHP / HarnessRouter as an execution *backend* behind the Execution
   Gateway, never as a layer on top of the control plane.** It runs work; it
   does not authorize it. AION dispatches to it *after* the `PolicyEngine`
   authorizes a command and classifies its risk — the same posture as ADR-003
   (Execution Gateway) and ADR-008 (authority is not probabilistic).

2. **We introduce a vendor-neutral `HarnessExecutionProvider` port in
   `@aion/core`**, mirroring the `FeatureGate` (ADR-010) and `DecisionProvider`
   (ADR-009) seams. Core depends on the port only; a UHP/HarnessRouter-backed
   provider is injected at the composition root. A `HarnessExecutionAdapter`
   bridges the port to the existing `ExecutionAdapter`, so a harness is
   dispatched like any other execution target and returns a normalized
   `ExecutionResult`.

3. **Boundary rules (normative):**
   - **Not authority.** Harness selection (`harnessId`) is a routing/
     optimization input, never permission. The `PolicyEngine` / Execution
     Gateway remain the sole authority.
   - **Fails closed — including on indeterminate outcomes.** Unlike the
     fail-open `FeatureGate`, harness execution is the running of authorized
     work: a *definite* provider failure is a first-class `failed` result,
     surfaced — never a silent success. But a **lost response or stream is not a
     definite failure**: HarnessRouter may have accepted the request and the
     remote harness may still be running shell/fs/git. Marking that terminal
     `failed` is not failing closed — it misreports the ledger and a naive retry
     duplicates side effects. So every dispatch carries a stable **idempotency
     key** across the seam, and an ambiguous outcome is recorded as
     **indeterminate** (not `failed`) and must be **reconciled or cancelled**
     against that key before it is declared terminal; a retry re-uses the key so
     the harness de-duplicates rather than re-executing.
   - **No raw tenant PII crosses the seam.** Context and files pass by reference;
     secrets pass as resolver references (`vault:…`, `$headers.…`) resolved at
     the composition root — Core never holds the secret value.
   - **Least privilege + budget + risk.** A harness runs under the risk tier,
     autonomy level, budget ceiling, tool/data scope, and tenant isolation the
     authorized command carries — nothing wider. High-blast-radius shell/fs/git
     execution is governed like any other execution, not exempt from it.
   - **Runner egress is deny-by-default.** All outbound network from the Runner
     is denied or explicitly allow-listed by the `aion-infra` network policy
     (the model provider host, and nothing else unless a task's tools require a
     named destination). Model traffic is not assumed to be the only egress; the
     shell/tools/CLIs are treated as capable of arbitrary outbound requests and
     confined accordingly.
   - **Governed rollout, fail-closed activation.** A harness is enabled behind a
     kill-switch (`harness.<id>.enabled`). Because it gates dispatch to an
     untrusted, high-blast-radius executor, this activation check is
     **fail-closed** — deny-by-default: a flag-lookup timeout or outage (or an
     activated kill-switch) leaves the harness **disabled**, the opposite of the
     fail-open `FeatureGate` posture used for ordinary governed work. Prefer a
     locally cached last-known state that defaults to disabled. Harness×model
     choices are measured through the decision-engine experiment (ADR-009) and
     the PostHog `DecisionRecord` mirror (ADR-010) before promotion.

4. **We treat HarnessRouter as an untrusted execution dependency.** Self-host in
   an isolated sandbox; pin versions; gate egress via the `aion-infra` network
   policy; review the upstream harness CLIs it installs on first launch (each
   under its own license). UHP is adopted as a *contract to build against*; the
   vendor implementation behind the port is replaceable.

## Alternatives Considered

- **Layer HarnessRouter on top of AION** — Rejected. It would put a third-party
  router above the authority boundary, inverting the trust model (the same
  reason PostHog and the FeatureGate sit beside/below authority, not above it).
- **Keep bespoke per-harness routing** — Viable, and the status quo. Rejected as
  the default because it does not standardize harness portability or make
  harness×model a measurable knob; retained as the fallback if the pilot's
  cost/latency and maintenance wins do not materialize.
- **Build our own harness protocol** — Rejected for now: UHP is Responses-
  compatible with a public conformance suite; re-implementing it is
  undifferentiated work. We build against UHP and keep the provider replaceable.
- **Adopt HarnessRouter Cloud as the execution home** — Deferred. Self-host
  first to keep keys/state/workspaces local (data-residency posture, ADR-010);
  revisit managed Cloud once the boundary and economics are proven.

## Consequences

### Positive

- Harness portability: new harness = a config entry behind one UHP contract, not
  a bespoke integration; less runtime/infra glue to maintain.
- Harness×model becomes a first-class, measurable routing knob feeding mission
  economics (cost per outcome) and the ADR-009/010 experiment + calibration loop.
- Managed sessions, workspaces, streaming, and cancellation via a Responses-
  compatible surface existing SDKs/UIs already speak.
- Self-host keeps provider keys and workspace state local, consistent with the
  data-residency posture.

### Negative

- A new, high-blast-radius execution dependency (real shell/fs/git, secret
  injection) that must run strictly under AION governance, sandboxing, and egress
  control.
- A young, fast-moving upstream plus the harness CLIs it installs — a supply-
  chain surface to pin and review.
- A UHP↔AION mapping layer (Responses shape ↔ Command/ExecutionResult) to own.
- If bespoke routing already suffices, this is a standardization bet: maintenance
  saved vs. one more dependency in the execution hot path.

## Implementation Notes

- **Seam (this decision, shipped):** `@aion/core`
  `ports/harness-execution-provider.ts` (`HarnessExecutionProvider` + types),
  `adapters/in-memory-harness-provider.ts` (deterministic test/dev provider),
  `adapters/harness-execution-adapter.ts` (bridge to `ExecutionAdapter`; resolves
  `harnessId` from `metadata`/`toolId`, overridable for capability/risk/
  experiment routing; fails closed).
- **Pilot next:** a UHP/HarnessRouter-backed provider at the composition root
  (runtime / action-engine), one task class behind `harness.<id>.enabled`,
  measured against outcomes before promotion. Prove the cost/latency claims on
  our own task classes — the upstream benchmark numbers are task-dependent by
  their own methodology.
  - **"Shadow" here is not ADR-009 shadow.** ADR-009 shadow is record-only — the
    decision engine computes but never acts. A harness *executes* real
    shell/fs/git, so running one "in shadow" on a live command would duplicate
    its side effects (a second CRM write, message, deployment, or repo
    mutation). So: shadow-compare harnesses only on an **isolated,
    read-only/eval task class** (no external writes), and for side-effectful
    commands use **canary-only** — a single, governed, real execution on the new
    harness for a slice of traffic, never a duplicate alongside the incumbent.
- Do not send governed-decision authority through a harness; the `PolicyEngine`
  authorizes, the harness executes.

## Follow-up Decisions

- Where the UHP-backed provider lives (aion-runtime vs aion-action-engine) and
  its sandbox / egress profile in `aion-infra`.
- The harness×model routing policy: static config, capability-based, or an
  ExperimentProvider arm; and how `harnessId`/`sessionId` are recorded on
  execution objects and mirrored to PostHog.
- Self-host vs HarnessRouter Cloud once the boundary and economics are proven.
