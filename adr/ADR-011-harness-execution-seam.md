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
workspaces local; only model calls egress to the configured provider.

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
   - **Fails closed.** Unlike the fail-open `FeatureGate`, harness execution is
     the running of authorized work: a provider error is a first-class `failed`
     result, surfaced — never a silent success. A dropped execution must be
     visible in the ledger.
   - **No raw tenant PII crosses the seam.** Context and files pass by reference;
     secrets pass as resolver references (`vault:…`, `$headers.…`) resolved at
     the composition root — Core never holds the secret value.
   - **Least privilege + budget + risk.** A harness runs under the risk tier,
     autonomy level, budget ceiling, tool/data scope, and tenant isolation the
     authorized command carries — nothing wider. High-blast-radius shell/fs/git
     execution is governed like any other execution, not exempt from it.
   - **Governed rollout.** A harness is enabled behind a `FeatureGate` kill-
     switch (`harness.<id>.enabled`) and its harness×model choices are measured
     through the decision-engine experiment + shadow calibration (ADR-009) and
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
  (runtime / action-engine), one task class in shadow/canary behind
  `harness.<id>.enabled`, measured against outcomes before promotion. Prove the
  cost/latency claims on our own task classes — the upstream benchmark numbers
  are task-dependent by their own methodology.
- Do not send governed-decision authority through a harness; the `PolicyEngine`
  authorizes, the harness executes.

## Follow-up Decisions

- Where the UHP-backed provider lives (aion-runtime vs aion-action-engine) and
  its sandbox / egress profile in `aion-infra`.
- The harness×model routing policy: static config, capability-based, or an
  ExperimentProvider arm; and how `harnessId`/`sessionId` are recorded on
  execution objects and mirrored to PostHog.
- Self-host vs HarnessRouter Cloud once the boundary and economics are proven.
