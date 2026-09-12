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
STATUS:                  done (research) / needs-review (Architect)
HANDOFF:                 see below
```

---

## Handoff

```text
MISSION                  RES-001
STATUS                   needs-review
SUMMARY                  Research brief recommends narrow Core contract (types only) for model.generate@1 + required telemetry; adapters stay in products; defer stream/tool_call and adaptive routing; kill vendor SDKs in Core/Runtime.
FINDINGS                 Product LlmProvider (Anthropic+OpenRouter) already proves the adapter pattern. Platform gap is AIO-16 telemetry/surface + governed Runtime visibility. PRE-OL proves stubs, not models.
IMPLEMENTATION           Docs only under .aion/research/
FILES CHANGED            .aion/research/*, .aion/missions/RES-001-*, .aion/README.md, agents/12-research-engineer.md
TESTS                    N/A
TEST RESULTS             N/A
ARCHITECTURE IMPACT      Potential future Core contract + ADR if Architect accepts; Runtime neutrality preserved
SECURITY IMPACT          None in this slice; future spikes must stay env-key-only
KNOWN LIMITATIONS        No prototype run; no live provider smoke in this slice
BLOCKERS                 Architect go/no-go
PR / COMMIT / BRANCH     cursor/research-prototype-charter-da87
PROOF                    Citations in RES-001 brief (intelligence layer, ADR-002, M007, PRE-OL, product OpenRouter design)
NEXT RECOMMENDED ACTION  Architect accept/reject recommendation; if accept, RESEARCH starts isolated products telemetry spike (prototype second)
```
