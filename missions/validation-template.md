# Validation Gate Template

The validation gate stands between shipping and calling a mission a **success**.
Releasing proves a thing is safe and ready; validation proves it actually
delivered the expected outcome for real users. Copy this template and complete
it after real-world exposure.

---

```markdown
# Validation Gate: <Mission name> (MISSION-XXX)

- **Prepared by:** <name>
- **Date:** <YYYY-MM-DD>
- **Validation window:** <from> – <to>
- **Decision:** CONTINUE | ITERATE | KILL | PLATFORMIZE

## Expected vs. Actual Outcome
- **Expected outcome (from mission):** <restate>
- **Actual outcome (from real data):** <what the outcome records/events show>
- **Gap:** <difference and why>

## Outcome Evidence
<Real outcomes from aion-data — events and outcome records, not opinions.
Prefer outcome metrics (revenue, retention, resolved cases) over volume.
See ../architecture/learning-loop.md.>

- Success metric 1: target vs. actual
- Success metric 2: target vs. actual

## Lessons
<Validated conclusions drawn from the outcomes. These become lessons in
aion-data. See ../architecture/data-layer.md.>

## Decision Rationale
<Why CONTINUE / ITERATE / KILL / PLATFORMIZE, grounded in the evidence above.>

## If PLATFORMIZE
- [ ] Reused by multiple products/missions (or clearly will be).
- [ ] Interface is understood.
- [ ] Behavior is stable enough to standardize.
- [ ] Centralizing reduces duplication more than it creates coupling.
(All four required — ../roadmap/platform-maturity.md#platformization-rule)

## If KILL
- **Lesson recorded:** <the durable lesson so the mistake is not repeated>
- **Cleanup:** <what is decommissioned>
```

---

## Notes

- **Validation is evidence-based.** The decision rests on real outcomes in
  `aion-data`, not on how the build felt.
- **KILL is a first-class, respected outcome.** A killed mission that produced a
  clear lesson is a success for the system. See
  [lifecycle.md](lifecycle.md).
- **PLATFORMIZE requires all four conditions.** Do not promote a component to
  `aion-core` just because the product succeeded. See
  [../roadmap/platform-maturity.md](../roadmap/platform-maturity.md#platformization-rule).
- **Outcomes feed learning.** Whatever the decision, outcomes and lessons are
  recorded and flow back through the
  [learning loop](../architecture/learning-loop.md).
