# Agent mission — RES-001 Model-provider contract research

```markdown
MISSION ID:              RES-001
TITLE:                   Model-provider integration contract — research & recommendation
BUSINESS OUTCOME:        Unblock honest OL-001 live-model proof without vendor lock-in
PRIMARY OWNER:           Research / Prototype Engineer (12)
SUPPORTING AGENTS:       Architect (review), AI Engineer (contract shape), Runtime (neutrality check)
REVIEWERS:               Architect, Security (secrets boundary)
REPOSITORY:              aion-docs (research artifacts only this slice)
BRANCH / WORKTREE:       cursor/research-prototype-charter-da87
CONTEXT:                 AIO-16 P0; product-local LlmProvider exists; PRE-OL lacks live model proof; M007 recommend-only
OBJECTIVE:               Produce decision-quality evidence: what to contract, where adapters live, what to kill/defer
ACCEPTANCE CRITERIA:
  - Existing ADRs/contracts inspected and cited
  - Vendor/library comparison with kill/promote criteria
  - Explicit recommendation: Core types vs product-only vs gateway
  - No production code / SDK merge in this slice
  - Next action named for Architect go/no-go
CONSTRAINTS:
  - Research first; no prototype in this slice
  - No production credentials
  - Do not import provider SDKs into Core/Runtime
DEPENDENCIES:            None for research slice; AIO-16 for later implementation
EXPECTED OUTPUT:         .aion/research/RES-001-*.md + backlog entry
TEST REQUIREMENTS:       N/A (docs-only)
SECURITY CONSIDERATIONS: No secrets; Runtime stays SDK-free
STATUS:                  done (research) / conditional-go (Architect) / prototype-complete (products spike)
HANDOFF:                 see below
```

## Handoff

```text
MISSION                  RES-001
STATUS                   needs-review → accepted (Architect CONDITIONAL GO); prototype-complete
SUMMARY                  Research brief + Architect CONDITIONAL GO + isolated products telemetry spike (safeGenerate, required telemetry, keyless tests). Core types deferred pending ADR. Runtime stays SDK-free.
FINDINGS                 Product LlmProvider pattern is sound. Platform gap was telemetry + governed visibility. PRE-OL still needs live model proof on Runtime path after ADR.
IMPLEMENTATION           Docs in aion-docs; prototype in aion-products (no Core/Runtime SDK changes)
FILES CHANGED            .aion/research/*, .aion/missions/RES-001-*, aion-products provider contracts/adapters/tests
TESTS                    aion-products: npm test + npm run typecheck (model-telemetry + openrouter green)
TEST RESULTS             pass
ARCHITECTURE IMPACT      Products-only for now; Core contract requires ADR
SECURITY IMPACT          Error codes redact secret-like fragments; no keys in spikes
KNOWN LIMITATIONS        No live provider smoke in CI; stream/tool_call deferred; no adaptive routing
BLOCKERS                 Architect ADR before Core types
PR / COMMIT / BRANCH     aion-docs: cursor/research-prototype-charter-da87; aion-products: cursor/res001-model-telemetry-spike-da87
PROOF                    RES-001 brief + architect decision + keyless telemetry tests
NEXT RECOMMENDED ACTION  Architect drafts short ADR for model.generate@1 types; AI/Runtime register only after ADR
```
