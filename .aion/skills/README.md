# Frontend agent skills

Portable skills that raise UI craft and engineering quality for AION Frontend /
UX work. Installed under both `.agents/skills/` (skills.sh layout) and
`.cursor/skills/` (Cursor discovery).

## Sources

| Source | What we took |
|---|---|
| [ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) | Design-system search, UX checklists, stack guidance |
| [taste-skill](https://github.com/Leonxlnx/taste-skill) | Anti-slop frontend taste, redesign audit, minimalist dial |
| [impeccable](https://github.com/pbakaus/impeccable) | `/impeccable` craft commands + craft-floor bans |
| [mattpocock/skills](https://github.com/mattpocock/skills) | TDD, code-review, diagnosing-bugs, architecture deepening |

## When to load which skill

| Situation | Skill(s) |
|---|---|
| New page / surface / visual world | `impeccable` → `init` / `shape` / new-work; then `ui-ux-pro-max` `--design-system` |
| Existing UI looks generic / AI-slop | `redesign-existing-projects` + `design-taste-frontend` |
| Operator console / dashboard density | `design-taste-frontend` (DENSITY dial) + `operator-ui-workflows.md` |
| Landing / desks commerce | `minimalist-ui` or soft editorial direction; never cream+terracotta default |
| Accessibility / touch / responsive audit | `ui-ux-pro-max` (`--domain ux`) + `impeccable audit` |
| Final ship pass | `impeccable polish` + `full-output-enforcement` |
| Feature with tests | `tdd` then `implement` |
| Bug / perf regression | `diagnosing-bugs` |
| Diff quality before merge | `code-review` |
| Module seams rotting | `improve-codebase-architecture` / `codebase-design` |
| Ambiguous UX brief | `grill-with-docs` |

## AION overrides (always win)

Skills improve craft. They do **not** override AION product truth:

1. **Attention loop** — state → decision → outcome ([operator-ui-workflows.md](../standards/operator-ui-workflows.md)).
2. **No invented KPIs** — empty/failing API → empty/error UI.
3. **Writes are governed** — Runtime / Stripe / product API only.
4. **No purple AI gradients / Inter-as-brand** — already banned; desks must leave cream/terracotta cluster.
5. **Operator Console is Operate mode** (impeccable) — scanability over spectacle.

## Refresh

```bash
# From aion-docs or aion-products
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend" --skill "redesign-existing-projects" --skill "minimalist-ui" -y
npx skills@latest add mattpocock/skills --skill "tdd" --skill "code-review" --skill "diagnosing-bugs" --skill "improve-codebase-architecture" -y
# ui-ux-pro-max: copy from upstream .claude/skills/ui-ux-pro-max into .agents/skills/
# impeccable: prefer `npx impeccable install --providers=cursor --scope=project`
#            (CLI zip may fail; fallback = copy .cursor/skills/impeccable from upstream)
```

Mirror `.agents/skills/<name>` → `.cursor/skills/<name>` after refresh so Cursor
loads the same files.
