# Action Tiers (Observe / Assist / Execute)

Product and policy-matrix vocabulary defined by
[ADR-007](../adr/ADR-007-observe-assist-execute-tiers.md).

**Action Tiers package authority for humans.**  
**Autonomy L0–L4 + Risk R0–R3 + PolicyEngine enforce authority for machines.**

```
AI agent → AION gateway (authn) → PolicyEngine (authz) → business API / CRM / DMS
```

Never: `AI agent → everything`.

## Tier definitions

| Tier | Intent | Allowed | Forbidden |
|---|---|---|---|
| **Observe** | Situational awareness | Search inventory, retrieve **approved** customer/context, summarize internal state | Any create/update/delete; send messages; money movement; schedule changes |
| **Assist** | Help a human decide | Draft messages, recommend offers, prepare records/payloads for review | Committing drafts; external sends; CRM/DMS mutations; workflow triggers with side effects |
| **Execute** | Perform approved work | Schedule, update CRM, trigger workflows **within explicit allow-lists** and earned grants | Anything outside grants; R3 without human gate; self-raising autonomy |

Financial, destructive, production-release, and security-sensitive actions stay
**human-gated** even when the agent’s tier is Execute
([risk-levels.md](risk-levels.md), [human-gates.md](human-gates.md)).

## Mapping to Autonomy L0–L4

| Action Tier | Max AutonomyLevel | Notes |
|---|---|---|
| Observe | L0 | Matches Core comment: read / search / analyze only |
| Assist | L1 | Matches Recommend: artifacts without side effects |
| Execute | L2–L4 | L2 reversible allow-list; L3 approval-bound; L4 earned waiver of R0–R2 only |

Platform maturity L0–L5 ([platform-maturity.md](../roadmap/platform-maturity.md))
is a **different** ladder — do not conflate in UI copy.

## Mapping to Principal roles (ADR-005)

| PrincipalRole | Typical use with Action Tiers |
|---|---|
| `invoke` | Observe / Assist / Execute agent-workers calling the gateway |
| `approve` | Human operators clearing Execute (and R3) gates — never the executing agent |
| `register` | Operators/services registering durable Actors and grants |

An Assist-tier agent principal must **not** hold ambient `approve`.

## Capability matrix (normative patterns)

Capabilities are dotted `domain.action` grants on the AgentActor. Tier is
violated when grants exceed the tier ceiling:

| Pattern | Observe | Assist | Execute |
|---|---|---|---|
| `*.read`, `*.search`, `*.analyze` | ✓ | ✓ | ✓ |
| `*.draft`, `*.recommend`, `*.prepare` | ✗ | ✓ | ✓ |
| `*.write`, `*.update`, `*.schedule`, `*.send` (non-R3) | ✗ | ✗ | ✓ (allow-listed) |
| `*.pay`, `*.delete`, `*.deploy`, security config | ✗ | ✗ | ✓ only via R3 gate |

`allowedTools` / `allowedData` / `forbiddenCapabilities` must be consistent with
the declared tier. Registration should **fail closed** on contradictions.

## Dealer / rental examples

| Workflow | Tier | Gate |
|---|---|---|
| Search inventory / retrieve approved customer profile | Observe | — |
| Draft sales follow-up / recommend financing offer text | Assist | Human edits/sends (or Promote→Execute send) |
| Create appointment in CRM | Execute | Policy; often R1/R2 |
| Update deal status / trigger remarketing workflow | Execute | Allow-list + tenant scope |
| Submit financing / issue refund / void contract | Execute + **R3 human gate** | Always |

## Electrical / accounting (same tiers)

| Vertical | Observe | Assist | Execute (gated where R3) |
|---|---|---|---|
| Electrical | Read job/estimate docs | Draft estimate narrative | Schedule crew; post invoice (gate payments) |
| Accounting | Read client docs in allowed classes | Draft notices / AP coding suggestions | File prep workflows; **never** unattended tax filing / wire without gate |

## Proof requirement

At least one automated proof must show:

1. Actor declared/grants Observe-only (or `autonomyLevel: L0`).
2. AuthorizationRequest for an Execute-class capability → `DENY` with
   `action-tier` check failed — even if permissions were over-granted.
3. Trust Score `permissionCompliance` fails when such a path terminalizes as
   `denied` (status `untrusted`).

Core implements this in `PolicyEngine.authorize` when `actionTier` is set or
autonomy is L0, and in `computeAgentTrustScore` (see `@aion/core` tests
`action-tier-trust-score.test.ts`).

## Enforcement note

Action Tier checks are enforced when `AgentActor.actionTier` is set, or when
`autonomyLevel` is `L0`. L1+ agents without an explicit Action Tier keep prior
authorize behavior so earned-autonomy Execute paths are not silently broken.

## Invariants

- Action Tier never grants more than AutonomyLevel + capability grants.
- R3 remains gated at every tier.
- Customer-facing chat UIs default to **Assist** unless Execute is explicitly
  enabled per workflow.
