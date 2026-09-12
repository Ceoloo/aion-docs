# ADR-004: `aion-desks` Repository Ownership

- **Status:** Proposed
- **Date:** 2026-09-12
- **Decision Owners:** Architect (ratify); Documentation Engineer (draft)
- **Related:** [ADR-001](ADR-001-greenfield-reset.md), [ADR-002](ADR-002-runtime-host-ownership.md),
  [`../repositories/aion-desks.md`](../repositories/aion-desks.md),
  [`../architecture/execution-platform-v1.md`](../architecture/execution-platform-v1.md)

## Context

The Cursor multi-root workspace and `.aion` ownership maps include **`aion-desks`**:
a Next.js + Stripe landing/checkout surface for packaged digital products
(Operator Desk, Everyday Desk).

The constitution still describes **six ADR-backed canonical repositories**:

`aion-docs` · `aion-core` · `aion-data` · `aion-runtime` · `aion-infra` · `aion-products`

`aion-desks` is therefore an **ownership ambiguity**:

1. It ships real product commerce code and is worked daily in the workspace.
2. It is **not** named in ADR-001/002 as part of the platform set.
3. Execution Platform v1 already treats desks as **commercial packages outside
   the execution kernel** ([execution-platform-v1.md](../architecture/execution-platform-v1.md)).
4. Docs PR #50 added [`repositories/aion-desks.md`](../repositories/aion-desks.md)
   as a **satellite** page and explicitly deferred canon membership to this ADR.

Without a recorded decision, agents will either:

- invent a seventh “canonical” repo without Architect ratification, or
- dump Stripe/landing logic into `aion-products` / `aion-runtime` by convenience.

That is repository-boundary drift — ADR-worthy under
[`README.md`](README.md) (“alters a repository boundary or ownership”).

## Decision

**Proposed (Docs recommendation for Architect ratification):**

We will keep **`aion-desks` as a permanent product-commerce satellite repository**
**outside** the six ADR-backed platform repositories.

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
   under a “workspace satellite” section; update maps after Accept.

Architect may **Accept**, **Accept with modifications**, or **Reject** in favor
of Alternative A or B below. Until Accepted, treat desks as satellite (current
docs posture).

## Alternatives Considered

- **Alternative A — Promote to seventh canonical repository**  
  Formally extend ADR-001’s set to seven.  
  *Not recommended:* desks is commerce UX, not a platform plane; inflating the
  canon weakens the “six planes” mental model without adding control-plane value.

- **Alternative B — Fold into `aion-products`**  
  Move the Next.js Stripe app under `aion-products` (e.g. `apps/desks`).  
  *Viable later:* reduces repo count; costs a migration and mixes SKU landing
  with Copilot/workforce surfaces. Prefer only if Product+Frontend want one
  product monorepo and accept the move cost.

- **Alternative C — Leave undocumentated / chat-only**  
  *Rejected:* violates Documentation Engineer principle — ownership must not
  live only in chat.

## Consequences

### Positive

- Removes ambiguity for Orchestrator routing and agent cards.
- Protects the six-repo platform boundary (no commerce logic in runtime/core).
- Matches Execution Platform v1 language (“outside execution kernel”).

### Negative

- Workspace has a seventh git root forever (cognitive overhead).
- Product engineers must remember “desks ≠ products monorepo” until/unless
  Alternative B is chosen later.
- Satellite status must be restated in maps so newcomers do not “fix” it.

## Implementation Notes

If **Accepted as proposed**:

| Artifact | Action |
|---|---|
| This ADR | Status → Accepted |
| [`repositories/README.md`](../repositories/README.md) | Keep satellite table; cite ADR-004 |
| [`repositories/aion-desks.md`](../repositories/aion-desks.md) | Replace “until ADR” with “per ADR-004” |
| [`.aion/architecture/repository-map.md`](../.aion/architecture/repository-map.md) | Cite ADR-004 |
| [`.aion/TEAM.md`](../.aion/TEAM.md) | No ownership change (Product + Frontend) |
| `aion-desks` README | One-line pointer to ADR-004 |

If **Alternative B** is chosen instead: open a Product/Frontend migration mission;
Architect supersedes this ADR with a follow-up.

## Follow-up Decisions

- Whether Desk fulfillment post-checkout becomes a governed Runtime mission
  (product/runtime concern — not decided here).
- Whether additional commerce landings (future SKUs) share `aion-desks` or get
  their own satellites (default: same repo until complexity forces split).
