# Agent mission — RES-002 Friday production research brief

```markdown
MISSION ID:              RES-002
TITLE:                   Map 2026-09-25 industry brief to AION certify-first plan
BUSINESS OUTCOME:        Raise Operator Loop acceptance to GOVERNED EXECUTION without new subsystems
PRIMARY OWNER:           Research / Prototype Engineer (12)
SUPPORTING AGENTS:       Architect (go/no-go), Runtime/Product (acceptance evidence)
REVIEWERS:               Architect
REPOSITORY:              aion-docs
BRANCH / WORKTREE:       cursor/research-prototype-charter-da87
CONTEXT:                 AWS Omni, Okta Blueprint, GitHub agentic-workflow study, anticipatory oversight, Snowflake agent observability
OBJECTIVE:               Evidence pack: what AION already has, what to certify next, what to kill/defer
ACCEPTANCE CRITERIA:
  - Each of 5 industry signals mapped to existing contracts
  - Explicit NO-GO list for large subsystems this week
  - GOVERNED EXECUTION acceptance checklist proposed
  - Field-by-field coverage of proposed execution-record additions
CONSTRAINTS:
  - Research only; no Core/Runtime/Product code in this slice
  - Mission before infrastructure; platformization rule
STATUS:                  done (research) / needs-review (Architect)
```

## Handoff

```text
MISSION                  RES-002
STATUS                   needs-review
SUMMARY                  Industry signals validate AION control plane. Recommend certify-first: upgrade Operator Loop live acceptance to GOVERNED EXECUTION using existing contracts; defer OTEL/Okta/Skills/contain subsystems.
FINDINGS                 ~70% of proposed certification fields already exist (Principal, DelegatedAuthority, Capability, M007 evals, economics, idempotency, outcomes). Gaps: envelope id stamp, policy_version stamp, human_intervention_seconds, full contain(), OTEL spans, SkillManifest.
IMPLEMENTATION           Docs only under .aion/research/
FILES CHANGED            .aion/research/RES-002-*, capability-backlog, missions/RES-002-*
TESTS                    N/A
ARCHITECTURE IMPACT      Possible thin ADR for Mission Authority Envelope after Architect GO
SECURITY IMPACT          None this slice; contain() design must preserve evidence
BLOCKERS                 Architect go/no-go on certify-first plan
PR / COMMIT / BRANCH     cursor/research-prototype-charter-da87
NEXT RECOMMENDED ACTION  Architect accept; Runtime/Product amend operator-loop-v1 evidence table and run live OL-001 against GOVERNED EXECUTION bar
```
