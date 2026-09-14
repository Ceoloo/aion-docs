# ADR-007: Observe / Assist / Execute Action Tiers

- **Status:** Accepted
- **Date:** 2026-09-14
- **Decision Owners:** Security / Permissions Engineer

## Context

Product and vertical packaging (especially dealer/rental, and TechOps client
conversations) need a simple three-tier story:

> Customer-facing AI ≠ unrestricted system authority.

The platform already has a richer model: Autonomy **L0–L4**, Risk **R0–R3**,
PolicyDecision `ALLOW|DENY|REQUIRE_APPROVAL`, and Principal roles
`invoke|approve|register` (ADR-005). Inventing a second autonomy ladder would
create dual L\* confusion (already a risk vs platform-maturity L0–L5).

Linear AIO-20; TechOps Monday Brief 2026-09-14.

## Decision

1. **We will adopt Observe / Assist / Execute as product Action Tiers** — a
   packaging and policy-matrix vocabulary for humans, not a replacement for
   AutonomyLevel L0–L4.
2. **Canonical enforcement remains L0–L4 + Risk + PolicyEngine.** Action Tiers
   are a **projection** of allowed capabilities / autonomy ceiling / gate rules.
3. **Mapping (normative):**

   | Action Tier | Autonomy ceiling | Allowed effects | Default gate |
   |---|---|---|---|
   | **Observe** | ≤ L0 | Read / search / analyze approved context only | None (R0/R1); DENY writes |
   | **Assist** | ≤ L1 | Drafts, recommendations, prepare records; **no** external/business mutations | None for drafts; R2+ still policy-checked |
   | **Execute** | L2–L4 | Side effects within allow-list + earned grants | R3 always gated; R2 per policy; L4 may waive R0–R2 only |

4. **Financial / destructive / security-sensitive actions are never “Execute
   without gate.”** They remain R3 → human gate regardless of Action Tier label.
5. **Detailed matrix and vertical examples** live in
   [governance/action-tiers.md](../governance/action-tiers.md).

## Alternatives Considered

- **Replace L0–L4 with three tiers in Core** — Rejected; collapses earned
  autonomy (L2/L3/L4 distinctions) and breaks Mission 008 evidence model.
- **Ignore product language; only expose L0–L4** — Rejected; too opaque for SMB
  sales and vertical packaging.
- **Map Assist = L2** — Rejected; Assist must remain non-mutating.

## Consequences

### Positive

- Clear client language: Observe ≠ Assist ≠ Execute.
- Preserves earned-autonomy ladder and R3 hard gate.
- Gives a proof target: Observe-tier agents cannot Execute.

### Negative

- Requires careful UI/docs discipline so “Execute” is not confused with L4
  unbounded autonomy (it is not).
- Agents may need explicit Action Tier tags in addition to autonomyLevel.

## Implementation Notes

- Document Action Tier on AgentActor metadata / product specs; optional Core
  enum `ActionTier` for validation only.
- Policy proofs: mission-style matrix that an Observe-scoped agent receiving an
  Execute capability request gets DENY.
- Dealer/rental examples in governance spec; other verticals reuse the same
  matrix with different capability catalogs.

## Follow-up Decisions

- Whether Runtime rejects registration when declared Action Tier contradicts
  granted capabilities (recommended: yes, fail closed).
- Desks/UI presentation of Action Tier on agent cards.
