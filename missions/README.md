# Missions

Missions are how AION builds things. **Products are built through missions**
([Principle 7](../engineering/principles.md)) — nothing significant is built
without one. A mission carries a problem, a user, an expected outcome, an
explicit scope, and — crucially — explicit non-goals.

## Documents

| Document | Purpose |
|---|---|
| [lifecycle.md](lifecycle.md) | The stages a mission moves through. |
| [mission-template.md](mission-template.md) | The template for defining a mission. |
| [release-template.md](release-template.md) | The release gate template. |
| [validation-template.md](validation-template.md) | The validation gate template. |
| [MISSION-RCAOS-001-revenue-capture-appointment-os.md](MISSION-RCAOS-001-revenue-capture-appointment-os.md) | Flagship Appointment OS v1 (demo client) — Defined |
| [MISSION-CREATIVE-001-creative-os-client-shorts.md](MISSION-CREATIVE-001-creative-os-client-shorts.md) | Creative OS v1: governed shorts for one AION services client — Defined (proposed) |
| [MISSION-DSL-001-data-sovereignty-licensing-validation.md](MISSION-DSL-001-data-sovereignty-licensing-validation.md) | Data Sovereignty & Licensing 90-day validation (P-AIO-9) — Defined |

## Why missions

- They enforce **Mission Before Infrastructure**: infrastructure is justified by
  a mission, not built speculatively.
- They make **scope and non-goals explicit**, which is how architectural drift
  is prevented at the source.
- They connect work to **outcomes and learning**, closing the
  [learning loop](../architecture/learning-loop.md).

## How to use this section

1. Define a mission with [mission-template.md](mission-template.md).
2. Move it through [lifecycle.md](lifecycle.md).
3. Pass the **release gate** ([release-template.md](release-template.md)) before
   shipping.
4. Pass the **validation gate** ([validation-template.md](validation-template.md))
   before deciding to continue, iterate, kill, or platformize.
