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
- The exact one-way mirror schema from the event ledger / `DecisionRecord`s to
  PostHog (which properties, how PII is stripped at the boundary).
- Experiment framework around decision-engine thresholds (ties to ADR-009).
- Whether feature-flag evaluation belongs in the Execution Gateway or the
  orchestrator, and how flag state is recorded on execution objects.
