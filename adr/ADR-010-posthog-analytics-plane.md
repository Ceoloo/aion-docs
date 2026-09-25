# ADR-010: Adopt PostHog as the Analytics / Experimentation Plane

- **Status:** Accepted
- **Date:** 2026-09-25
- **Decision Owners:** Founder / CEO; Product Engineer

## Context

AION has a strong internal telemetry story for **machine labor**: the event
ledger, mission economics (cost per outcome, autonomy yield, agent margin),
the decision-engine shadow evaluator (ADR-009), immutable execution receipts,
and Watchtower. What it has **no** coverage for is the other half of the
system:

- how **humans** use the product surfaces (Revenue Copilot, Desks, dashboards)
  — funnels, activation, drop-off, UX friction;
- safe, progressive **rollout** and instant **kill-switches** for agents and
  new capabilities;
- measuring whether a change (a decision threshold, a prompt variant, an agent
  strategy) actually **improved outcomes**;
- app/service **error tracking** and cheap **LLM cost/latency** drill-down.

PostHog (open-source, MIT core, self-hostable) provides all of these in one
suite: product & web analytics, session replay, feature flags, experiments,
surveys, error tracking, logs, AI/LLM observability, a data warehouse, and a
CDP. Adopting it consolidates ~6 tools we would otherwise build or buy.

The tension: AION's own thesis is provenance, least privilege, and "authority
cannot be probabilistic." AION handles tenant CRM/PII. Shipping that data to a
third-party analytics cloud would contradict the architecture and create a
compliance surface. So the value is real but the integration must be bounded.

## Decision

1. **We adopt PostHog as the analytics / experimentation / rollout plane that
   sits BESIDE the AION control plane, never inside its trust boundary.** It
   observes humans, apps, and model calls. It is not, and must never become,
   the authority, the audit ledger, or the calibration source of truth.

2. **Boundary rules (normative):**
   - PostHog **never authorizes** anything. The `PolicyEngine` / Execution
     Gateway (ADR-003, ADR-008) remain the sole authority. Feature flags are a
     *rollout gate and kill-switch*, evaluated as an **input** to the
     orchestrator — never as permission.
   - PostHog is **not** the system of record. Immutable execution receipts and
     the audit trail stay in `aion-data`; `DecisionRecord`s stay authoritative
     in AION. Anything sent to PostHog is a **one-way mirror** for analysis.
   - **No tenant PII leaves AION into PostHog.** No contact/lead emails, names,
     phone numbers, or company names as event properties or in replays.
     Identity uses stable non-PII ids; tenants are PostHog **groups**.
   - Deployment respects data residency: EU cloud or self-host, with a
     same-origin reverse proxy; DNT respected; autocapture off on CRM surfaces;
     session replay masked (all inputs + text) and off by default.

3. **Phased rollout:**
   - **Phase 1 — surfaces we're blind on (now):** `posthog-js` in Revenue
     Copilot (and next the dashboard / Desks) for product analytics, sanitized
     pageviews, surveys, and client feature flags. PII-safe by construction.
   - **Phase 2 — rollout control + experimentation:** server-side flags
     (`posthog-node`) in `aion-runtime` / `aion-action-engine` to gate agent
     and decision-plane rollouts; experiments on decision-engine confidence
     thresholds and prompt/agent variants, measured against real outcomes (the
     natural partner to ADR-009 shadow calibration).
   - **Phase 3 — observability + BI:** AI/LLM observability and error tracking
     in the runtime and adapters (mirror, don't replace mission economics);
     CDP/warehouse export of ledger + economics rollups to power client value
     dashboards.

4. **Deferred / excluded:** PostHog "self-driving mode" (an autonomous agent
   that opens PRs against our product) is **not** adopted now — it must be
   wrapped by AION governance before it can act, and until then is advisory
   only.

## Implementation status (2026-09-25)

- **Phase 1 — shipped.** `posthog-js` in Revenue Copilot with the PII-safe
  config (autocapture off, sanitized manual pageviews, replay off by default,
  `/ingest` same-origin proxy); no-op unless `NEXT_PUBLIC_POSTHOG_KEY` is set.
- **Phase 2 — partially shipped.** The vendor-neutral *seams* are in; the
  PostHog-backed, live rollout control that Phase 2 also calls for is not.
  - **Kill-switch input (seam shipped):** a `FeatureGate` port + `StaticFeatureGate`
    in `aion-core`, consulted by the Orchestrator **before** policy as an input
    (fail-open, bounded) — a switched-off agent domain is withheld pre-dispatch
    and emits `command.rejected` with `policyId: feature-gate.kill-switch`. It
    can withhold, never grant; the `PolicyEngine` stays the sole authority.
  - **Experimentation (shipped):** an `ExperimentProvider` seam over the
    decision-engine's confidence→route thresholds, with `evaluateShadowByVariant`
    for per-arm calibration. The engine runs in **shadow mode** at the
    action-service approval gate, recording what auto-approve *would* have
    decided with the human decision as ground truth.
  - **Pending:** a PostHog-backed `FeatureGate` provider behind the seam, and a
    running service that composes the `aion-core` Orchestrator so flags gate
    *live* dispatch. Until then the seam is exercised only by `StaticFeatureGate`
    (config/env-driven) and there are no live gate reads — so PostHog-driven
    rollout control / kill-switch is not yet available in a deployed path.
- **One-way DecisionRecord mirror — shipped** (resolves a Follow-up below).
  The action-service streams shadow `DecisionRecord`s to PostHog server-side
  through a vendor-neutral sink (the decision-engine stays SDK-free; the client
  is injected at the composition root). Off by default; a no-op unless the
  **server-side** `POSTHOG_API_KEY` is set. Events:
  - `aion_decision_shadow_recorded` — route, confidence, risk, decision type,
    experiment key + variant, latency, cost (non-PII, explicitly enumerated).
  - `aion_decision_shadow_settled` — ground truth and whether the shadow
    **agreed** with the human. The deciding human (`humanOverride.by`) is
    **never** emitted.

Still ahead in **Phase 3**: AI/LLM observability + error tracking in the
runtime/adapters, and CDP/warehouse export of ledger + economics rollups.
Streaming **FeatureGate flag reads** lands once a service composes the
`aion-core` Orchestrator (no live gate reads exist to mirror until then).

## Alternatives Considered

- **Build on the existing event ledger / Watchtower for everything** — Rejected
  for the human-facing gaps: session replay, no-code funnels, flags, and
  experiments are large products in their own right. Reuse AION telemetry for
  machine labor; adopt PostHog for humans/apps/models.
- **Point tools (Amplitude + LaunchDarkly + Sentry + …)** — Rejected on cost
  and integration overhead for an early-stage system; PostHog consolidates them
  and is self-hostable if data residency demands it.
- **PostHog as the calibration/economics source of truth** — Rejected;
  duplicates and would erode AION's audit guarantees. Mirror one-way only.
- **PostHog cloud with default autocapture** — Rejected for CRM surfaces; it
  would capture contact PII from the DOM. Autocapture off + manual sanitized
  events instead.

## Consequences

### Positive

- Immediate visibility into how reps actually use Revenue Copilot; a basis for
  activation/retention work.
- Safe progressive rollout + kill-switch for agents without touching the
  authority layer.
- Experiments make the decision-engine calibration measurable in production.
- One vendor instead of many; self-hostable if needed.

### Negative

- A third-party data processor in scope — requires ongoing PII discipline,
  residency choices, and a DPA.
- Two "measurement" systems coexist (AION economics + PostHog); docs must keep
  the source-of-truth boundary clear to avoid drift.
- Feature flags add a rollout surface that must be governed so it never becomes
  a way to bypass policy.

## Implementation Notes

- **Phase 1 (this decision):** `Ceoloo-aion-revenue-copilot` —
  `lib/analytics/posthog.ts` (PII-safe options + `identifyActor`),
  `app/providers.tsx` (`AnalyticsProvider`, no-op unless
  `NEXT_PUBLIC_POSTHOG_KEY` set), `next.config.ts` `/ingest` reverse proxy,
  documented `.env.example`. Autocapture off; manual sanitized pageviews;
  replay off by default; `person_profiles=identified_only`.
- Verify `aion-infra` network policy permits egress to the chosen PostHog host
  before enabling in any deployed environment.
- Server SDKs and LLM observability (`aion-runtime`, `aion-action-engine`) land
  in later phases; do not send governed-decision authority through PostHog.

## Follow-up Decisions

- Cloud region vs self-host, and the DPA / data-residency posture.
- ~~The exact one-way mirror schema from the event ledger / `DecisionRecord`s to
  PostHog (which properties, how PII is stripped at the boundary).~~ **Decided**
  for `DecisionRecord`s — see the shipped `aion_decision_shadow_*` events under
  Implementation status. The event-ledger mirror (execution receipts / economics
  rollups) remains open, to be settled with the Phase-3 CDP/warehouse export.
- Experiment framework around decision-engine thresholds (ties to ADR-009).
- Whether feature-flag evaluation belongs in the Execution Gateway or the
  orchestrator, and how flag state is recorded on execution objects.
