# Release Gate Template

The release gate stands between **Build** and shipping. A mission passes only
when it is [production-ready](../engineering/production-readiness.md) and
governed. Copy this template and complete it before release.

---

```markdown
# Release Gate: <Mission name> (MISSION-XXX)

- **Prepared by:** <name>
- **Date:** <YYYY-MM-DD>
- **Decision:** PASS | FAIL | CONDITIONAL

## Production Readiness
- [ ] Serves the mission; traces to MISSION-XXX.
- [ ] Single canonical owner assigned.
- [ ] In the correct repository; respects dependency rules.
- [ ] Contracts (API / event / data) explicit and to standard.
- [ ] Least-privilege permissions in place.
- [ ] Risk classified; high-risk actions gated.
- [ ] Observable (traceability spine emitted).
- [ ] Tested (deterministic paths + failures) and green in CI.
- [ ] Evals exist and pass for AI-driven behavior (no regression).
- [ ] Cost controls in place (for agents/model use).
- [ ] Fails safe; destructive actions reversible or gated.
- [ ] No secrets committed.
(Full checklist: ../engineering/production-readiness.md)

## Governance
- [ ] Required human gates configured. (../governance/human-gates.md)
- [ ] Change reaches production via change management. (../governance/change-management.md)
- [ ] Architecturally significant decisions recorded as ADRs. (../adr/README.md)

## Rollback
- **Rollback plan:** <how this is reversed if it goes wrong>
- **Blast radius:** <what is affected if it fails>

## Outstanding conditions (if CONDITIONAL)
<List conditions that must be met, with owners and dates.>

## Sign-off
- **Release approver:** <name> — for high-risk releases this is a human gate. (../governance/authority-model.md)
```

---

## Notes

- **PASS requires the full checklist.** Partial compliance is `CONDITIONAL` at
  best, with named conditions and owners.
- **The release gate is not the validation gate.** Passing release means "safe
  and ready to ship," not "proven valuable." Value is judged later, at the
  [validation gate](validation-template.md).
- **High-risk releases are a human gate.** See
  [../governance/human-gates.md](../governance/human-gates.md).
