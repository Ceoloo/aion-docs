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
BLOCKING?:  No (docs already treat desks as satellite; ADR removes ambiguity)
CONTEXT:    Follow-up from merged aion-docs PR #50 (docs lag sync)
PRIORITY:   Medium — boundary clarity before desks grows more SKUs
STATUS:     open
DELIVERED IN: adr/ADR-004-aion-desks-repo-ownership.md (Proposed)
```

## Mission

```text
MISSION
STATUS                  needs-review
SUMMARY                 PR #50 merged. Docs posture treats aion-desks as satellite.
                        Proposed ADR-004 asks Architect to ratify permanent
                        satellite ownership (recommended) vs promote vs fold.
FINDINGS                Desks is Next.js+Stripe commerce only; Execution Platform
                        v1 already says commercial packages outside kernel.
IMPLEMENTATION          Draft ADR-004 + this handoff (Docs). No code moves.
FILES CHANGED           adr/ADR-004-*.md, adr/README.md, this handoff
TESTS                   n/a (docs)
ARCHITECTURE IMPACT     Repository boundary clarification (six-repo canon)
SECURITY IMPACT         None direct; keeps Stripe surface out of control plane
KNOWN LIMITATIONS       Recommendation is Docs'; not Architect Accept yet
BLOCKERS                Architect decision
PR / COMMIT / BRANCH    (this PR)
PROOF                   Linked ADR draft + desks README/repo map
NEXT RECOMMENDED ACTION Architect reviews ADR-004 and sets Status
```

## Architect decision checklist

- [ ] Read [`../../adr/ADR-004-aion-desks-repo-ownership.md`](../../adr/ADR-004-aion-desks-repo-ownership.md)
- [ ] Choose: **Accept proposed** | **Alternative A (7th canon)** | **Alternative B (fold into products)**
- [ ] Update ADR Status line + any map citations
- [ ] Close this handoff (`STATUS: accepted`) with pointer to commit/PR

## Starter for Architect chat

Paste from [`.aion/agents/STARTERS.md`](../agents/STARTERS.md) (`AION — ARCHITECT`), then:

> Review Proposed ADR-004 (`aion-desks` ownership). Ratify satellite vs seventh
> canon vs fold into `aion-products`. Do not leave the decision in chat only.
