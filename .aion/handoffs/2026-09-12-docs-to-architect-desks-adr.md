# Handoff — DOCS → ARCHITECT: `aion-desks` ownership ADR

```text
HANDOFF ID: 2026-09-12-docs-to-architect-desks-adr
NEED:       Ratify repository ownership for aion-desks (satellite vs canon vs fold)
FROM:       Documentation / Knowledge Engineer (AION — DOCS)
FOR:        Architect / Code Reviewer (AION — ARCHITECT)
CONTRACT:
  - Accept, modify, or reject Proposed ADR-004
  - On Accept: flip Status to Accepted; ensure maps cite ADR-004
  - On fold-into-products: supersede with migration mission + new ADR status
  - Do not leave ownership chat-only
BLOCKING?:  No
CONTEXT:    Follow-up from merged aion-docs PR #50; review PR #52
PRIORITY:   Medium
STATUS:     accepted
DECISION:   Accept proposed — permanent satellite outside six-repo platform set
DELIVERED IN: adr/ADR-004-aion-desks-repo-ownership.md (Accepted)
ACCEPTED BY: AION — ARCHITECT (2026-09-12)
PROOF:      aion-desks package.json = next/react/stripe only; EP v1 outside kernel
```

## Architect decision checklist

- [x] Read ADR-004
- [x] Choose: **Accept proposed** (reject A / defer B / reject C)
- [x] Update ADR Status → Accepted + map citations
- [x] Close this handoff

## Mission close-out

```text
MISSION
STATUS                  done
SUMMARY                 ADR-004 Accepted: aion-desks is a permanent product-commerce
                        satellite; six-repo platform canon unchanged.
FINDINGS                No platform package imports in aion-desks; EP v1 already
                        places commercial packages outside the execution kernel.
                        Seventh-canon promotion rejected; fold-into-products deferred.
IMPLEMENTATION          ADR Accepted + repository/map/handoff citations updated.
ARCHITECTURE IMPACT     Boundary clarified — commerce ≠ platform plane
SECURITY IMPACT         Stripe surface stays out of control plane by ownership rule
NEXT RECOMMENDED ACTION Merge PR #52; add ADR-004 one-liner to aion-desks README
```
