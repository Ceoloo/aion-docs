# ADR-004: `aion-desks` Repository Ownership

- **Status:** Accepted
- **Date:** 2026-09-12
- **Decision Owners:** Architect (accepted); Documentation Engineer (draft)
- **Related:** [ADR-001](ADR-001-greenfield-reset.md), [ADR-002](ADR-002-runtime-host-ownership.md),
  [`../repositories/aion-desks.md`](../repositories/aion-desks.md),
  [`../architecture/execution-platform-v1.md`](../architecture/execution-platform-v1.md)
- **Supersedes:** informal “six repos only / desks unmentioned” ambiguity after
  desks entered the Cursor workspace

## Context

The Cursor multi-root workspace and `.aion` ownership maps include **`aion-desks`**:
a Next.js + Stripe landing/checkout surface for packaged digital products
(Operator Desk, Everyday Desk).

The constitution describes **six ADR-backed canonical platform repositories**:

`aion-docs` · `aion-core` · `aion-data` · `aion-runtime` · `aion-infra` · `aion-products`

`aion-desks` created an **ownership ambiguity**:

1. It ships real product commerce code and is worked in the workspace.
2. It is **not** named in ADR-001/002 as part of the platform set.
3. Execution Platform v1 already treats desks as **commercial packages outside
   the execution kernel** ([execution-platform-v1.md](../architecture/execution-platform-v1.md)).
4. Docs PR #50 added [`repositories/aion-desks.md`](../repositories/aion-desks.md)
   as a satellite page and deferred canon membership to this ADR.
5. **Architect inspection (2026-09-12):** `aion-desks` depends only on Next.js,
   React, and Stripe — no imports of `aion-core`, `aion-data`, or `aion-runtime`.
   That matches a commerce satellite, not a seventh platform plane.

Without a recorded decision, agents would invent a seventh “canonical” repo or
dump Stripe/landing logic into `aion-products` / `aion-runtime` by convenience.

## Decision

**We keep `aion-desks` as a permanent product-commerce satellite repository
outside the six ADR-backed platform repositories.**

Concretely:

1. **Platform canon remains six repos.** No seventh platform plane.
2. **`aion-desks` owns** marketing/landing pages and Stripe Checkout/webhooks for
   Desk SKUs only.
3. **`aion-desks` does not own** control-plane, canonical schema, Execution
   Gateway, infra provisioning, or Revenue Copilot engines.
4. **Dependency rule:** `aion-desks` may call public/product APIs and Stripe; it
   must not import `aion-core` / `aion-data` as a parallel OS, and must not host
   execution policy.
5. **Docs:** keep [`repositories/aion-desks.md`](../repositories/aion-desks.md)
   under a “workspace satellite” section; maps cite this Accepted ADR.

## Alternatives Considered

- **Alternative A — Promote to seventh canonical repository**  
  Formally extend ADR-001’s set to seven.  
  **Rejected:** desks is commerce UX, not a platform plane; inflating the canon
  weakens the six-plane model without control-plane value. Code inspection shows
  no platform kernel coupling that would justify canon elevation.

- **Alternative B — Fold into `aion-products`**  
  Move the Next.js Stripe app under `aion-products` (e.g. `apps/desks`).  
  **Deferred, not chosen now:** reduces repo count later, but costs migration and
  mixes SKU landing with Copilot/workforce surfaces. Revisit only if Product +
  Frontend explicitly want a single product monorepo and fund the move.

- **Alternative C — Leave undocumented / chat-only**  
  **Rejected:** ownership must not live only in chat.

## Consequences

### Positive

- Removes ambiguity for Orchestrator routing and agent cards.
- Protects the six-repo platform boundary (no commerce logic in runtime/core).
- Matches Execution Platform v1 (“outside execution kernel”).
- Matches observed dependency graph (Stripe/Next only).

### Negative

- Workspace keeps a seventh git root (cognitive overhead).
- Product engineers must remember “desks ≠ products monorepo” until/unless
  Alternative B is chosen later.
- Satellite status must stay visible in maps so newcomers do not “fix” it.

## Implementation Notes

| Artifact | Action |
|---|---|
| This ADR | Status → **Accepted** (this revision) |
| [`repositories/README.md`](../repositories/README.md) | Satellite table cites ADR-004 Accepted |
| [`repositories/aion-desks.md`](../repositories/aion-desks.md) | “per ADR-004 Accepted” |
| [`.aion/architecture/repository-map.md`](../.aion/architecture/repository-map.md) | Cite ADR-004 Accepted |
| [`.aion/TEAM.md`](../.aion/TEAM.md) | No ownership change (Product + Frontend) |
| `aion-desks` README | One-line pointer to ADR-004 |

## Follow-up Decisions

- Whether Desk fulfillment **after** checkout becomes a governed Runtime mission
  (product/runtime — not decided here).
- Whether additional commerce landings share `aion-desks` or split (default: same
  repo until complexity forces a split).
- Optional later: Alternative B monorepo fold (new ADR if chosen).
