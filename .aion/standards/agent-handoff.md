# Agent Handoff Standard

Every specialist finishes a mission (or a meaningful slice) with this block.

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
```

## Cross-agent dependency handoff

When work depends on another specialist, create
[`../handoffs/TEMPLATE.md`](../handoffs/TEMPLATE.md):

```text
NEED
FROM
FOR
CONTRACT
BLOCKING?               (Yes/No)
```

Example:

```text
NEED:     Canonical ExecutionRecord schema
FROM:     Data Engineer
FOR:      Runtime Engineer
CONTRACT: execution_id, workflow_id, status, started_at, completed_at, cost, outcome
BLOCKING: Yes
```

## Communication rule

Agents communicate through **artifacts and contracts** (missions, handoffs,
ADRs, PR descriptions, schema diffs) — not through unspoken assumptions.
