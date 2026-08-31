# Mission Template

Copy this template to define a mission. A mission is not ready to leave
**Mission Definition** until every field is filled. Empty fields are how drift
and speculative work sneak in — do not skip them.

---

```markdown
# Mission: <short name>

- **Mission ID:** MISSION-XXX
- **Owner:** <accountable human/team>
- **Status:** Idea | Defined | Research | Architecture | Build | Released | Validated | Continued | Iterating | Killed | Platformized
- **Date:** <YYYY-MM-DD>

## Problem
<The specific problem this mission solves. One paragraph. Concrete, not aspirational.>

## Target User
<Who has this problem. Be specific — a role/segment, not "everyone".>

## Expected Outcome
<The real-world result that would make this mission a success. Outcome, not output.>

## Scope
<What this mission WILL do. Bounded.>

## Non-Goals
<What this mission will explicitly NOT do. This section is mandatory — it prevents scope creep and drift.>

## Success Metrics
<How success is measured. Prefer outcome metrics (revenue, retention, resolved cases) over volume metrics (messages sent).>

## Architecture Impact
<Which repositories/boundaries this touches. Any new contracts. Any ADRs required. See ../repositories/dependency-rules.md.>

## Data Requirements
<What canonical data it reads/writes. Any new entities (owned by aion-data). Any new events. See ../engineering/data-contracts.md.>

## Security Considerations
<Identities, permissions, data classification, external comms, destructive actions. See ../architecture/security-model.md.>

## Release Gate
<The specific conditions that must be true to ship. Reference ./release-template.md.>

## Validation Gate
<How real commercial/user validation will be judged. Reference ./validation-template.md.>

## Learning Requirements
<What outcomes will be collected and what we expect to learn. See ../architecture/learning-loop.md.>
```

---

## Notes

- **Non-Goals are mandatory.** A mission without explicit non-goals is not
  defined.
- **Metrics should be outcome-oriented.** "Proposals sent" is volume;
  "deals won" is outcome. See [Principle 6](../engineering/principles.md).
- **Architecture Impact must respect boundaries.** If a mission wants to put code
  in the wrong repository, the mission is wrong, not the boundary. See
  [../repositories/dependency-rules.md](../repositories/dependency-rules.md).
- **Legacy is reference, not requirement.** Prior Aion-Sys work may inform the
  problem/domain, but is never a dependency. See
  [../legacy/README.md](../legacy/README.md).
