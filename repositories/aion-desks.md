# aion-desks

**Role:** Commercial desk landing / checkout surface (Stripe) for packaged AION
digital products.

> **Canon note:** `aion-desks` is present in the Cursor multi-root workspace and
> in [`.aion` ownership maps](../.aion/TEAM.md), but it is **not** one of the six
> ADR-backed canonical platform repositories. Ownership decision is
> **[ADR-004 (Proposed)](../adr/ADR-004-aion-desks-repo-ownership.md)** —
> Docs recommends permanent satellite; Architect must ratify. Do not put
> control-plane, canonical schema, or Execution Gateway logic here.

## Owns

- marketing / landing pages for Operator Desk and Everyday Desk
- Stripe Checkout session creation and webhook handling for those products
- Vercel-facing env wiring for Stripe price IDs (secrets never committed)

## Does NOT own

- Company OS / orchestration (`aion-core`)
- Canonical data / events (`aion-data`)
- Runtime host / Execution Gateway (`aion-runtime`)
- Cloud provisioning (`aion-infra`)
- Revenue Copilot engines / workforce control (`aion-products`)

## Specialist owners

Primary: Product + Frontend. Assist: Integrations (Stripe). See
[`.aion/TEAM.md`](../.aion/TEAM.md).

## Status

Shipped as a Next.js Stripe landing. Keys and Price IDs come from environment
variables only. See the repo README for local preview and webhook setup.

## Related

- [dependency-rules.md](dependency-rules.md) — platform dependency direction
  (desks must not become a parallel control plane)
- [../.aion/architecture/repository-map.md](../.aion/architecture/repository-map.md)
