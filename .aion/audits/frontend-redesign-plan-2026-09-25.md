# Frontend redesign — audit & implementation plan

**Date:** 2026-09-25  
**Owner:** Frontend / UX Engineer  
**Skills applied:** ui-ux-pro-max, design-taste-frontend, redesign-existing-projects, impeccable (Operate/Persuade modes), mattpocock code-review/tdd discipline  
**Surfaces:** `workforce-control`, `aion-products/web`, `aion-desks`

---

## Verdict

| Surface | Craft | Operator value | Generic-AI risk |
|---|---|---|---|
| Operator Console | Strong industrial identity | Attention rail good; **KPI wall dilutes it** | Low (olive/amber, Space Grotesk) |
| Revenue Copilot | Distinct ink/teal cockpit | Launch readiness strong; **Control Room silent on errors** | Medium (pills, glow, “A” mark) |
| Desks | Honest copy, lean stack | Buy path clear | **High — cream/serif/terracotta cluster** |

Highest leverage: collapse Command Center metrics, fix Control Room errors/attention, re-skin Desks off the default cream aesthetic, quiet Copilot chrome and strengthen the brand mark.

---

## Audit summary (evidence-backed)

### Operator Console (`workforce-control`)

- **Keep:** graphite/olive/signal amber tokens; Space Grotesk + IBM Plex; `WorkflowState` (Load/Error/Empty/AttentionRail); mobile nav.
- **Fix:** `HoldingOverview.tsx` stacks ~30 metric tiles after the attention rail — violates attention-loop standard.
- **Fix:** IE-001 list/detail still on ad-hoc loading/error text.
- **Perf:** eager page imports; Google Fonts CDN; no `prefers-reduced-motion`.

### Revenue Copilot (`web`)

- **Keep:** room model Launch → Live → Debrief → Control; readiness strip honesty; Syne/Manrope.
- **Fix:** Control Room `.catch(() => {})` swallows dashboard errors; 9-cell metric grid before mission attention.
- **Fix:** Brand mark is interchangeable “A” tile; Launch hero is product-generic after removing nav.
- **Quieter:** Sparkles, atlas-glow, nested panels, pill clusters on Debrief.

### Desks (`aion-desks`)

- **Keep:** draft-only honesty; BuyButton busy/error; success/cancel outcomes.
- **Fix:** `globals.css` `--paper: #f6f1e6` + system Palatino + gold — exact cream/terracotta AI bias to avoid.
- **Fix:** Brand is nav-scale only; flat background; no motion; weak focus styles.

### Cross-app gaps

No shared tokens/package; three type systems; duplicated shadcn trees; `WorkflowState` console-only; inconsistent attention grammar.

---

## Recommended visual direction (family)

**Shared DNA:** signal amber = “act”; monospace for IDs/evidence; no purple; no Inter; honest empty/error; AION wordmark family.

| Surface | Mode (impeccable) | Direction |
|---|---|---|
| Operator Console | **Operate** | Keep industrial palette + tight radius. Density for decisions, not KPI wallpaper. |
| Revenue Copilot | **Operate** (live) | Cooler ink + amber + teal live. Soften glow/pills. Full “Revenue Copilot” lockup, not “A”. |
| Desks | **Persuade** | Leave cream/terracotta. Warm-neutral or cool paper + ink + single shared amber/gold. Loaded display serif + sans. Brand-first hero + one visual artifact. |

---

## Implementation plan

### Phase 0 — Skills in workflow (this PR)

- [x] Install curated skills into `aion-docs` + `aion-products` (`.agents/skills` + `.cursor/skills`)
- [x] Document skill router (`.aion/skills/README.md`)
- [x] Point Frontend agent card at skills + this plan
- [ ] Optional: `npx impeccable init` → root `PRODUCT.md` per UI app (follow-up PR)

### Phase 1 — P0 operator value (1 PR per app preferred)

**Status:** implemented on `aion-products` branch `cursor/workflow-polish-phase1-ux-r1-r3-53e8`.

| Ticket | Repo | Work | Acceptance |
|---|---|---|---|
| UX-R1 | products | ✅ Collapse `HoldingOverview` to Attention → Act → Outcomes (missions list). One optional compact rollup with drill-through; remove duplicate KPI sections. | First viewport answers “what needs me?”; no duplicate success-rate tiles |
| UX-R2 | products | ✅ Control Room: surface load errors + retry; lead with failing/open missions / unmet gates before metrics | No silent `.catch`; error is actionable |
| UX-R3 | products | ✅ IE-001 pages use shared `WorkflowState` | Load/Error/Empty parity with Command |
| UX-R4 | desks | ✅ New token/type system; brand-first hero; atmosphere (subtle grain/gradient); focus-visible — branch `cursor/desks-reskin-ux-r4-53e8` | Escapes cream/terracotta; brand test passes without nav |

**Skills to invoke:** `redesign-existing-projects`, `impeccable distill` / `quieter` / `clarify`, `operator-ui-workflows`.

### Phase 2 — Brand & composition

**Status:** implemented on `aion-products` branch `cursor/workflow-polish-phase2-ux-r4-53e8`.

| Ticket | Repo | Work |
|---|---|---|
| UX-R5 | products/web | ✅ Replace “A” mark with AION / Revenue Copilot lockup; Launch hero brand-first |
| UX-R6 | products/web | ✅ Quiet LaunchPad (glow/Sparkles/nested panels); Debrief status as one line not pill strip |
| UX-R7 | products | ✅ Mission Control: list-first; KPI strip secondary (collapsible) |
| UX-R8 | products | ✅ Mobile bottom nav includes IE-001 |

**Skills:** `design-taste-frontend`, `impeccable typeset` / `layout` / `bolder` (Copilot mark) / `quieter` (glow).

### Phase 3 — System, performance, a11y

| Ticket | Repo | Work |
|---|---|---|
| UX-R9 | products (+ optional desks) | Extract shared operator state kit or document copy-contract; stop duplicating shadcn without tokens |
| UX-R10 | products | `React.lazy` route/room code-split; self-host or `font-display: optional`; `prefers-reduced-motion` |
| UX-R11 | desks | 2–3 intentional motions; focus rings; keyboard CTA affordances |
| UX-R12 | docs | `DESIGN.md` per surface via `impeccable document` after Phase 1 lands |

**Skills:** `impeccable audit` / `optimize` / `harden`; `tdd` for state kit; `code-review` on each PR.

---

## Suggested execution order

```text
Week slice A: UX-R1 + UX-R2 + UX-R3   (operator value, products)
Week slice B: UX-R4                   (desks identity)
Week slice C: UX-R5–R8                (brand/composition)
Week slice D: UX-R9–R12               (system + perf + docs)
```

Each ticket: grill brief → redesign skill diagnose → implement → `impeccable polish` → `code-review` → PR with desktop+mobile evidence.

---

## Non-goals

- New backend contracts for Console convenience
- Policy-aware retry UI until Runtime exposes eligibility
- Unifying dark ops + dark copilot into one theme (keep temperature differentiation)
- Decorative chart libraries without outcome semantics

---

## Related

- [operator-ui-workflows.md](../standards/operator-ui-workflows.md)
- [skills/README.md](../skills/README.md)
- [05-frontend-engineer.md](../agents/05-frontend-engineer.md)
