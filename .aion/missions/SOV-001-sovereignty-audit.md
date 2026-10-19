# Agent mission — SOV-001 Sovereignty audit (Phase 1 / P0)

```markdown
MISSION ID:              SOV-001
TITLE:                   Sovereignty audit — inventory providers, outbound APIs, data/model deps, credentials, IP exposure
BUSINESS OUTCOME:        Know every exposure path before migration; classify compliant / migrate / optional-external / exception-ADR
PRIMARY OWNER:           Architect (10)
SUPPORTING AGENTS:       Security (08), Infrastructure (06), Integrations (07), Data (02), Runtime (01), AI Engineer (03)
REVIEWERS:               Architect, Security, CEO
REPOSITORY:              aion-docs (audit artifact); inventory spans all repos
BRANCH / WORKTREE:       cursor/sovereign-intelligence-doctrine-ff59
CONTEXT:                 ADR-016 Sovereign Intelligence Doctrine adopted 2026-10-19; Phase 1 of sovereignty-program.md
OBJECTIVE:               Produce a complete exposure inventory and prioritized Phase 2 backlog — no untested cutovers
ACCEPTANCE CRITERIA:
  - Inventory covers core, data, runtime, infra, products, desks, AI tooling paths
  - Every item classified: compliant | migrate | optional-external | exception-ADR-required
  - Stack directions addressed: Supabase, Vercel, frontier APIs, Runtime, RAG/memory, GHL/CRM, AI tools
  - Priority-ordered migration backlog for Phase 2
  - No production shutdowns in this mission
CONSTRAINTS:
  - Audit and classify only; no untested provider shutdown
  - No secrets in the audit artifact (reference secret *names* / locations only)
  - Existing integrations are not automatically compliant
DEPENDENCIES:            ADR-016 Accepted; doctrine + sovereignty-program published
EXPECTED OUTPUT:         .aion/audits/sovereignty-inventory-YYYY-MM-DD.md + Phase 2 backlog list
TEST REQUIREMENTS:       N/A (docs/audit)
SECURITY CONSIDERATIONS: Treat inventory itself as sensitive; limit distribution of detailed exposure maps
STATUS:                  done (audit complete 2026-10-19); awaiting Architect/Security/CEO review
HANDOFF:                 see below
```

## Doctrine references

- [architecture/sovereign-intelligence-doctrine.md](../../architecture/sovereign-intelligence-doctrine.md)
- [adr/ADR-016-sovereign-intelligence-doctrine.md](../../adr/ADR-016-sovereign-intelligence-doctrine.md)
- [roadmap/sovereignty-program.md](../../roadmap/sovereignty-program.md)

## Artifact

- [../audits/sovereignty-inventory-2026-10-19.md](../audits/sovereignty-inventory-2026-10-19.md)

## Handoff

```text
MISSION                  SOV-001
STATUS                   done — needs-review (Architect, Security, CEO)
SUMMARY                  Cross-repo sovereignty inventory complete. Control plane + local Postgres SoR largely compliant; material gaps are frontier LLM prompt egress (products), legacy Supabase island, Vercel delivery, incomplete offline/local inference, AI-tooling IP policy, and ADR-010 PostHog vs ADR-016 tension.
FINDINGS                 P0: Anthropic/OpenRouter proprietary path; Restricted External Gateway policy; Supabase legacy retirement. P1: self-host UI; AI tooling policy; Web Speech; PostHog/GitHub exception ADRs. Runtime has no production outbound LLM — products do.
IMPLEMENTATION           Docs-only audit; no production changes
FILES CHANGED            .aion/audits/sovereignty-inventory-2026-10-19.md; this mission; roadmap/sovereignty-program.md Phase 1 checks
TESTS                    N/A
TEST RESULTS             N/A
ARCHITECTURE IMPACT      Unlocks Phase 2 missions SOV-002..006
SECURITY IMPACT          Maps IP egress; no secrets published
KNOWN LIMITATIONS        No live VPS/Supabase object enumeration; Supabase schema inventory deferred to SOV-004
BLOCKERS                 None for Phase 2 kickoff after review
PR / COMMIT / BRANCH     aion-docs: cursor/sovereign-intelligence-doctrine-ff59
PROOF                    .aion/audits/sovereignty-inventory-2026-10-19.md
NEXT RECOMMENDED ACTION  Review audit → open SOV-002 (local inference) + SOV-003 (gateway IP policy) as P0
```
