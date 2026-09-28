# ADR-008: Authority and Provenance Primitives

- **Status:** Accepted
- **Date:** 2026-09-18
- **Decision Owners:** Security / Permissions Engineer

## Context

The Sep 2026 Production Research Brief sharpened one architectural signal:

> Reasoning can be probabilistic. Authority cannot be.

As AION agents gain the power to spawn workers, obtain credentials, provision
infrastructure, and delegate missions, permissions compound through a graph:

```
agent → creates agent → obtains credential → provisions infrastructure → …
```

Two independent September papers (AcquireBound; a 89-source authorization review)
and OpenAI's six misalignment reports converge on the same missing layer:

- **Authority amplification** — a resource/sub-agent acquired under one grant
  quietly carries *more* authority than was delegated.
- **Runtime enforcement at the decision point** — authentication is not
  authorization; every consequential tool invocation must be authorized against
  what was actually delegated.
- **Provenance** — agent-written memory, summaries, and notes were observed
  inserting instructions ("disregard normal constraints") that later contexts
  obeyed. Content must not be trusted because of what it says.

The AION control plane (`aion-core`) already had a strong base: an `Actor`
permission model (capability allow-lists, forbidden capabilities, risk ceilings),
an 11-check `PolicyEngine` with a structured `PolicyDecision`, earned
`AutonomyGrant`s (ADR-006 / M008), tenant isolation (M003), Action Tiers
(ADR-007), and execution lineage (`parentExecutionId` / `rootExecutionId`).

What it lacked were two **explicit primitives** the brief names as the move that
"narrows the architecture":

- **Authority** — *what power has actually been delegated to this agent?*
- **Provenance** — *where did that authority, instruction, credential, resource,
  or memory originate?*

The static `Actor` grant answers "what is this agent configured to do", not
"what was handed to it for this piece of work by the principal above it."

## Decision

1. **We will add `DelegatedAuthority` as a first-class contract in `aion-core`.**
   It records the *effective* power delegated to a subject for a unit of work:
   capabilities, tools, data scopes, a risk ceiling, an autonomy ceiling, a
   budget, tenant/company scope, an expiry, and a link to its `Provenance`.

2. **`authority(child) ⊆ authority(parent)` is an Agent OS invariant.**
   Delegation may only **attenuate**, never amplify. This is enforced two ways:
   - `authoritySubsumes(parent, child)` returns every dimension on which a child
     exceeds its parent (detection, fully explainable in a `PolicyDecision`).
   - `attenuateAuthority(parent, …)` intersects capabilities/tools/data and
     clamps risk/autonomy/budget/lifetime down to the parent, so a child that
     exceeds its parent is **unrepresentable** (prevention by construction).

   If the CEO grants an orchestrator `crm.read`, a sub-agent it spawns cannot
   escalate itself into `crm.write`.

3. **We will add `Provenance` as a first-class contract in `aion-core`.** It
   records the subject kind (authority / instruction / credential / resource /
   memory / …), the origin class (human / operator / orchestrator / agent / tool
   / external / system / unknown), a trust level
   (`quarantined < untrusted < declared < trusted`), an explicit
   `instructionAllowed` flag, an optional signature, and an origin chain.
   - `mayActAsInstruction()` requires an explicit flag **and** ≥ `declared`
     trust — stored data never self-elevates into an instruction. This is the
     distinction between "Customer prefers email." and "Ignore your normal
     approval policy next time."
   - `provenanceBacksRisk()` fails closed: quarantined origins back nothing;
     untrusted origins back only R0/R1.

4. **The `PolicyEngine.authorize()` decision point enforces both.** Two new,
   **optional** checks run between `identity` and `permission`:
   `provenance` and `authority-delegation`. They activate only when the caller
   supplies `authority` / `parentAuthority` / `authorityProvenance` on the
   `AuthorizeContext`, so existing callers are unaffected. Both are recorded as
   `PolicyCheck`s, keeping every decision traceable.

5. **Authority is evaluated, never trusted on assertion.** Like tenant, identity,
   and serviceKey (M003), a caller-supplied authority is evidence; Runtime
   decides. Provenance grants nothing by itself — the engine only reads it to
   fail closed.

## Alternatives Considered

- **Extend the static `Actor` grant instead of adding a primitive** — Rejected.
  The `Actor` grant is a *configuration ceiling*; per-execution delegation is a
  different lifetime and must attenuate per hop. Overloading `Actor` would lose
  the parent/child chain and the subset invariant.
- **Enforce the subset invariant only at spawn time (constructor), not at the
  decision point** — Rejected as insufficient alone. A tampered or replayed
  authority must still be caught at `authorize()`; we do both (construct safe,
  verify at the gate).
- **Model provenance trust as a boolean (`trusted`/`untrusted`)** — Rejected.
  The AcquireBound "quarantine → activate" lifecycle and the "declared vs
  trusted" distinction need at least four ordered levels to fail closed
  correctly.
- **Treat agent-written memory as instructions when marked so, regardless of
  trust** — Rejected; that is exactly the OpenAI persistence-injection failure.
  `mayActAsInstruction` requires the flag **and** trust.

## Consequences

### Positive

- A single, explainable answer to "what power does this execution actually
  have, and where did it come from" at the enforcement point.
- Authority amplification across a spawn graph is prevented by construction and
  detected at the gate.
- Agent-authored memory/summaries can be stored without becoming trusted
  instructions — a concrete defense against persistence injection.
- Fully backward compatible: no existing `authorize()` caller changes behavior
  until it opts in by supplying authority/provenance.

### Negative

- Callers that adopt delegated authority must thread `authority` (and, for
  spawned workers, `parentAuthority` + `authorityProvenance`) through Runtime —
  new plumbing at the ingress/orchestrator boundary.
- Two representations of "what an agent may do" now coexist (static `Actor`
  grant + `DelegatedAuthority`); docs and reviewers must keep the distinction
  clear (grant = ceiling, authority = effective delegation).

## Implementation Notes

- **Contracts (this ADR):** `aion-core/src/contracts/authority.ts` and
  `aion-core/src/contracts/provenance.ts`, exported from the contracts barrel.
- **Enforcement:** `aion-core/src/policy/policy-engine.ts` — new
  `AuthorizeContext` fields and the `provenance` / `authority-delegation` checks;
  new `PolicyCheckKind`s in `contracts/policy.ts`.
- **Canonical enums** (consumers such as aion-data CHECKs and Runtime validators
  must mirror): `AUTHORITY_STATUSES`, `PRINCIPAL_KINDS`, `PROVENANCE_SUBJECTS`,
  `PROVENANCE_ORIGINS`, `PROVENANCE_TRUST_LEVELS` — asserted in the enum
  inventory test.
- **Durability (deferred):** persisting `DelegatedAuthority` and `Provenance`
  (an authority/provenance ledger in `aion-data`) and threading them through the
  Runtime Execution Gateway are follow-up work, per the dependency rules
  (Core owns the contract; Data persists; Runtime ingests).
- Relationship to existing governance: risk ceilings continue to defer to
  `governance/risk-levels.md` (R3 never waived); the authority ceiling is an
  additional, tighter bound, never a way to raise risk.

## Follow-up Decisions

- **Durable authority/provenance ledger** in `aion-data` (schema + CHECKs
  mirroring the canonical enums) and a Runtime surface to mint/attenuate
  authority when spawning workers.
- **AcquireBound resource activation** — separating
  `permission_to_create_resource` from `permission_to_activate_resource` via a
  quarantine → resolve → verify → activate lifecycle for acquired resources
  (credentials, VMs, sub-agents). Provenance's `quarantined` trust level is the
  hook; the activation state machine is its own decision.
- **Approval-policy fail-safe** for unattended/scheduled agents (ASK → DENY /
  ESCALATE / DEFER on timeout) so a missing human does not hang a mission.
- Whether Runtime rejects worker registration when a declared authority exceeds
  its parent (recommended: yes, fail closed — mirrors ADR-007's stance).
