# Agent Mission Template

Copy to `.aion/missions/<MISSION-ID>-<slug>.md` (or keep in the PR / orchestrator
chat if ephemeral). Fill every field before deep implementation.

---

```markdown
MISSION ID:
TITLE:
BUSINESS OUTCOME:
PRIMARY OWNER:
SUPPORTING AGENTS:
REVIEWERS:
REPOSITORY:
BRANCH / WORKTREE:
CONTEXT:
OBJECTIVE:
ACCEPTANCE CRITERIA:
CONSTRAINTS:
DEPENDENCIES:
EXPECTED OUTPUT:
TEST REQUIREMENTS:
SECURITY CONSIDERATIONS:
STATUS:
HANDOFF:
```

## Field guidance

| Field | Meaning |
|---|---|
| **BUSINESS OUTCOME** | Real-world result (revenue, reliability, deploy speed) — not “ship code” |
| **PRIMARY OWNER** | Exactly one specialist role |
| **SUPPORTING AGENTS** | Assist only; list contracts they own |
| **REVIEWERS** | Usually QA; add Security/Architect when risk/boundary applies |
| **ACCEPTANCE CRITERIA** | Observable checks; include restart/audit if claiming durability |
| **CONSTRAINTS** | Non-goals, deadline, env limits, “do not touch X” |
| **DEPENDENCIES** | Link handoff IDs if blocked on another agent |
| **HANDOFF** | Final block per [`../standards/agent-handoff.md`](../standards/agent-handoff.md) |

## Ownership example

```text
MISSION:   Add durable execution recovery
OWNER:     Runtime Engineer
ASSIST:    Data Engineer
REVIEW:    QA Engineer, Security Engineer
ARCHITECTURE REVIEW: Architect
```
