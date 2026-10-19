# Agent mission — SOV-001 Sovereignty audit (Phase 1 / P0)

```markdown
MISSION ID:              SOV-001
TITLE:                   Sovereignty audit — inventory providers, outbound APIs, data/model deps, credentials, IP exposure
BUSINESS OUTCOME:        Know every exposure path before migration; classify compliant / migrate / optional-external / exception-ADR
PRIMARY OWNER:           Architect (10)
SUPPORTING AGENTS:       Security (08), Infrastructure (06), Integrations (07), Data (02), Runtime (01), AI Engineer (03)
REVIEWERS:               Architect, Security, CEO
REPOSITORY:              aion-docs (audit artifact); inventory spans all repos
BRANCH / WORKTREE:       (open when execution starts)
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
STATUS:                  ready (doctrine landed); not started
HANDOFF:                 see below
```

## Doctrine references

- [architecture/sovereign-intelligence-doctrine.md](../../architecture/sovereign-intelligence-doctrine.md)
- [adr/ADR-016-sovereign-intelligence-doctrine.md](../../adr/ADR-016-sovereign-intelligence-doctrine.md)
- [roadmap/sovereignty-program.md](../../roadmap/sovereignty-program.md)

## Handoff (when complete)

```text
MISSION                  SOV-001
STATUS                   <fill>
SUMMARY                  <fill>
FINDINGS                 <fill>
FILES CHANGED            <fill>
NEXT RECOMMENDED ACTION  Open Phase 2 missions per backlog priority
```
