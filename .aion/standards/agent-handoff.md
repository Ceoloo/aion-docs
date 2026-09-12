# Agent Handoff Standard

Agents communicate through **artifacts and contracts**, not chat replay.

There are two related formats:

1. **Mission completion** — end-of-slice report for humans / Orchestrator
2. **Structured agent handoff** — machine-valid payload (`AgentHandoff` in
   `aion-core`) for agent-to-agent delegation

Use (2) whenever work crosses an agent boundary. Use (1) when closing a mission
slice. A completion report may *embed* a structured handoff JSON block.

Canonical schema: `@aion/core` → `AgentHandoff`
(`aion-core/src/contracts/agent-handoff.ts`).

---

## Structured agent handoff (required for A2A)

Required fields:

| Field | Meaning |
|---|---|
| `facts` | Established statements (not guesses) |
| `uncertainties` | Unknowns / disputes; mark `blocking` when the receiver must not decide |
| `artifactRefs` | Pointers to files, PRs, executions, schemas — never pasted transcripts |
| `confidence` | Overall score in `[0, 1]` that facts + recommendation are safe to act on |

Also required for a valid Core object: `schemaVersion` (`"1"`), `handoffId`,
`kind`, `need`, `createdAt`.

Recommended: `decisionNeeded`, `recommendedAction`, `fromLabel` / `toLabel`
(or agent ids), `confidenceGate`, `contract`, `blocking`.

### JSON shape (schemaVersion 1)

```json
{
  "schemaVersion": "1",
  "handoffId": "hof_…",
  "kind": "delegation",
  "status": "open",
  "fromLabel": "AION — AI",
  "toLabel": "AION — RUNTIME",
  "need": "Accept AgentHandoff as Runtime command payload",
  "facts": [
    {
      "statement": "Zod contract lives in aion-core",
      "evidenceRefs": ["art_spec"]
    }
  ],
  "uncertainties": [
    {
      "statement": "Transport path not wired",
      "impact": "medium",
      "blocking": false,
      "resolveBy": "Runtime maps AgentHandoff into Command.payload"
    }
  ],
  "artifactRefs": [
    {
      "id": "art_spec",
      "kind": "file",
      "ref": "aion-core/src/contracts/agent-handoff.ts",
      "label": "AgentHandoff schema"
    }
  ],
  "confidence": 0.82,
  "confidenceGate": {
    "escalateBelow": 0.5,
    "autoAcceptAbove": 0.85
  },
  "decisionNeeded": "Accept as platform handoff contract?",
  "recommendedAction": "Plan Runtime ingestion behind existing Command path",
  "blocking": false,
  "createdAt": "2026-09-12T03:40:00.000Z"
}
```

### Confidence disposition (defaults)

| Condition | Disposition |
|---|---|
| Any `uncertainties[].blocking === true` | `escalate` |
| `confidence < escalateBelow` (default 0.5) | `escalate` |
| `confidence >= autoAcceptAbove` (default 0.85) | `accept` |
| Otherwise | `proceed_with_caution` |

Helpers in Core: `dispositionForHandoff`, `handoffRequiresHumanReview`.

### Rules

- **No chat dumps.** Summarize into facts / uncertainties / refs.
- **Facts ≠ guesses.** Put weak claims in `uncertainties`.
- **Artifacts by reference.** Prefer paths, PR URLs, execution ids.
- **Deterministic first.** If a fixed contract or code path suffices, do not
  invent an agent hop — hand off a `dependency` contract instead.
- **Do not redefine the schema in Runtime or Products.** Import from Core.

Markdown wrapper for Cursor specialists:
[`../handoffs/TEMPLATE.md`](../handoffs/TEMPLATE.md).

---

## Mission completion handoff

Every specialist finishes a mission (or a meaningful slice) with this block:

```text
MISSION
STATUS                  (done | blocked | needs-review | partial)
SUMMARY
FINDINGS
IMPLEMENTATION
FILES CHANGED
TESTS
TEST RESULTS
ARCHITECTURE IMPACT
SECURITY IMPACT
KNOWN LIMITATIONS
BLOCKERS
PR / COMMIT / BRANCH
PROOF
NEXT RECOMMENDED ACTION
STRUCTURED_HANDOFF_JSON (optional — AgentHandoff when A2A continuation)
```

## Cross-agent dependency handoff

When work depends on another specialist, create
[`../handoffs/TEMPLATE.md`](../handoffs/TEMPLATE.md) and include a valid
`AgentHandoff` JSON (`kind: "dependency"`).

Minimum human-readable header:

```text
NEED
FROM
FOR
CONTRACT
BLOCKING?               (Yes/No)
CONFIDENCE              (0–1)
```

Example:

```text
NEED:       Canonical ExecutionRecord schema
FROM:       Data Engineer
FOR:        Runtime Engineer
CONTRACT:   execution_id, workflow_id, status, started_at, completed_at, cost, outcome
BLOCKING:   Yes
CONFIDENCE: 0.9
```

## Communication rule

Agents communicate through **artifacts and contracts** (missions, handoffs,
ADRs, PR descriptions, schema diffs) — not through unspoken assumptions.
