# ADR-016: Sovereign Intelligence Doctrine

- **Status:** Accepted
- **Date:** 2026-10-19
- **Decision Owners:** Ceoloo (Founder / CEO)

## Context

AION’s platform foundation (docs → core → data → infra → runtime → products)
was built with deliberate vendor neutrality at the *capability* level — model
providers and cloud services sit behind interfaces. In practice, day-to-day
operation still leans on hosted dependencies for persistence (e.g. Supabase),
delivery (e.g. Vercel), public inference (frontier model APIs), and third-party
systems of engagement (e.g. GHL).

That posture creates unacceptable strategic risk for an intelligence company:

- proprietary data, workflows, and knowledge can leave AION’s controlled
  boundary through public APIs;
- core operations can fail when a provider, network path, or hosted auth
  service is unavailable;
- frontier model APIs can become a de facto mandatory dependency;
- recovery after a provider shutdown is unproven.

The goal is not merely an “AI-powered company.” It is an **independent
intelligence infrastructure** AION owns and controls — while still allowing
optional external services for explicitly non-proprietary tasks.

## Decision

**We adopt the AION Sovereign Intelligence Doctrine as a binding core
architecture principle**, effective 2026-10-19.

The full doctrine lives at
[../architecture/sovereign-intelligence-doctrine.md](../architecture/sovereign-intelligence-doctrine.md).
In force:

1. **Five non-negotiables:** data sovereignty; intelligence sovereignty;
   offline-first execution; zero unauthorized IP exposure; vendor independence.
2. **Target architecture:** Sovereign Control Plane, Intelligence Engine
   (local LLMs / RAG / router), Execution Runtime, Owned Data Layer,
   Infrastructure Layer, and a **Restricted External Gateway** (default deny).
3. **Acceptance gate:** every critical development proposal must answer the five
   sovereignty questions (IP boundary, offline core, model replaceability,
   data/key/history control, provider-shutdown recovery). Failures are
   noncompliant until redesigned or explicitly classified as optional
   non-proprietary external functions.
4. **Migration posture:** existing integrations are not automatically compliant.
   They require an exposure audit and controlled migration — **not** an
   untested shutdown.
5. **Practical starting point:** suitably licensed open-weight models on
   controlled hardware plus owned knowledge, evals, and execution logic. AION
   does not need to train every foundation model from scratch to own its
   intelligence infrastructure.

**Compressed principle:**

> We do not rent the foundation of our intelligence. We own the infrastructure,
> preserve our knowledge, control execution, and decide when — or whether — the
> outside world participates.

## Alternatives Considered

- **Continue hosted-first with stronger contracts only.** Rejected: contracts
  alone do not prevent IP exposure through public inference APIs, nor do they
  guarantee offline operation or recovery after provider shutdown.
- **Immediate hard cutover off Supabase / Vercel / frontier APIs.** Rejected:
  untested shutdown of critical paths violates reliable-outcomes governance and
  risks production breakage. Doctrine requires audit-then-migrate.
- **Train all foundation models in-house before declaring sovereignty.**
  Rejected: unnecessary for ownership of infrastructure, knowledge, evals, and
  execution. Licensed open-weight local inference is the practical starting
  point.
- **Sovereignty as guidance only (non-binding).** Rejected: without an
  acceptance gate, convenience will reintroduce mandatory external dependencies.

## Consequences

### Positive

- Clear, reviewable gate for architecture and product proposals.
- Aligns intelligence, data, runtime, and infra roadmaps on independence.
- Preserves optional external use for non-proprietary work behind policy.
- Compatible with existing “capabilities over vendors” and model-router seams.

### Negative

- Near-term delivery friction: some features that are easy via public APIs
  become noncompliant for proprietary tasks.
- Migration cost for hosted persistence, delivery, and inference paths.
- Offline and recovery drills require dedicated engineering time (Phase 3).
- Some prior vendor ADRs will need supersession or time-bounded exception
  records as the sovereignty program proceeds.

## Implementation Notes

- Doctrine text and acceptance gate:
  [../architecture/sovereign-intelligence-doctrine.md](../architecture/sovereign-intelligence-doctrine.md)
- Phased program (audit → independent core → offline → optimization → portable
  customer infra):
  [../roadmap/sovereignty-program.md](../roadmap/sovereignty-program.md)
- Affects all six canonical repos plus `aion-desks` and any AI development
  tooling paths that touch protected IP.
- Restricted External Gateway builds on the Execution Gateway seam
  ([ADR-003](ADR-003-execution-gateway-into-runtime.md)); add outbound IP /
  proprietary-data policy checks — do not invent a parallel bypass path.
- Intelligence layer updates: local inference as the default for proprietary
  work; public frontier APIs only for classified non-proprietary tasks.
- Stack directions (Supabase, Vercel, frontier APIs, Runtime, RAG, GHL, AI
  tools) are migration targets, not overnight deletions.

## Follow-up Decisions

- Phase 1 sovereignty audit mission (inventory + exposure classification) —
  open as a tracked mission under the sovereignty program.
- Self-hosted persistence / identity / secrets ADRs as Phase 2 choices mature
  (capability first; vendor only when selected).
- Local model runtime and model-router ADR for proprietary vs non-proprietary
  routing policy.
- Explicit supersession or exception ADRs for prior hosted-vendor decisions
  that conflict after audit (e.g. analytics/delivery paths) — do not silently
  rewrite history.
- Offline certification criteria (Phase 3) as a production-readiness extension.
