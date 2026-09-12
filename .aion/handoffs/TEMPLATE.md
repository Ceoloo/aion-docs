# Handoff Template

Copy to `.aion/handoffs/<from>-to-<for>-<slug>.md` or paste into mission/PR.

This template is the Cursor rendering of `@aion/core` `AgentHandoff`
(schemaVersion `1`). Keep the JSON block valid — prose above it is for humans.

---

```markdown
HANDOFF ID:             (hof_… or leave blank for minting)
KIND:                   (completion | delegation | dependency)
NEED:
FROM:
FOR:
CONTRACT:
BLOCKING?:              (Yes/No)
CONFIDENCE:             (0.0–1.0)
DECISION NEEDED:
RECOMMENDED ACTION:
CONTEXT:
DUE / PRIORITY:
STATUS:                 (open | accepted | rejected | superseded | closed)
DELIVERED IN:           (PR / commit / path)

## Facts
- …

## Uncertainties
- … (mark BLOCKING when the receiver must not decide)

## Artifact refs
- kind | ref | label

## AgentHandoff JSON
```json
{
  "schemaVersion": "1",
  "handoffId": "hof_pending",
  "kind": "dependency",
  "status": "open",
  "fromLabel": "",
  "toLabel": "",
  "need": "",
  "facts": [],
  "uncertainties": [],
  "artifactRefs": [],
  "confidence": 0.0,
  "decisionNeeded": "",
  "recommendedAction": "",
  "contract": "",
  "blocking": false,
  "createdAt": ""
}
```
```

## Example

```text
NEED:       AgentHandoff Runtime transport
FROM:       AI / Agent Engineer
FOR:        Runtime Engineer
CONTRACT:   Command.payload may carry AgentHandoff; no schema fork
BLOCKING?:  No
CONFIDENCE: 0.8
```

```json
{
  "schemaVersion": "1",
  "handoffId": "hof_example_runtime_transport",
  "kind": "dependency",
  "status": "open",
  "fromLabel": "AION — AI",
  "toLabel": "AION — RUNTIME",
  "need": "Transport AgentHandoff via existing Command path",
  "facts": [
    {
      "statement": "AgentHandoff Zod contract is exported from @aion/core",
      "evidenceRefs": ["art_core"]
    }
  ],
  "uncertainties": [
    {
      "statement": "Whether Runtime should validate on ingress or trust Core.parse at the edge",
      "impact": "medium",
      "blocking": false,
      "resolveBy": "Runtime Engineer chooses validation boundary"
    }
  ],
  "artifactRefs": [
    {
      "id": "art_core",
      "kind": "file",
      "ref": "aion-core/src/contracts/agent-handoff.ts",
      "label": "AgentHandoff contract"
    }
  ],
  "confidence": 0.8,
  "decisionNeeded": "Confirm Runtime ingress validation approach",
  "recommendedAction": "Parse with AgentHandoff at gateway edge; store handoffId on execution metadata",
  "contract": "AgentHandoff schemaVersion 1 — import from @aion/core only",
  "blocking": false,
  "createdAt": "2026-09-12T03:40:00.000Z"
}
```

## Acceptance

The **FOR** agent accepts when they can implement against the contract without
guessing field names, nullability, or ownership — and when
`dispositionForHandoff` is not `escalate` unless a human explicitly accepts the
risk.
