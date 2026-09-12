# Git Standards (Cursor agents)

## Branching

- Feature work on dedicated branches; prefer `cursor/<short-description>-<id>`
  in Cloud Agent environments, or the team’s agreed prefix.
- One mission / coherent change per branch when practical.
- Do not force-push shared branches without explicit human approval.

## Commits

- Clear, descriptive messages focused on **why**.
- Prefer small, reviewable commits over giant mixed bags.
- Never commit secrets, vendor dumps, or unrelated reformatting.

## Pull requests

- One primary owner listed in the PR body (agent role + human if known).
- Link mission ID when applicable.
- Include: summary, test commands + results, risks, rollback notes.
- Request reviewers per mission card (QA / Security / Architect as needed).

## Merge order (typical)

1. Docs / ADRs that define the contract (if required)
2. Data schema / migrations
3. Core contracts
4. Runtime / gateway
5. Product / frontend
6. Infra wiring
7. QA proof follow-ups

Orchestrator recommends merge order when multiple PRs land together.

## Multi-agent safety

- Separate branches (or worktrees) per specialist when parallelizing.
- Avoid two agents committing to the same branch/files without coordination.
- Handoffs are artifacts — not assumptions in chat history alone.
