<!--
TEMPLATE: intent.md  (SDLC stage 1 — Plan)
Codex usage:
  - Fill this WITH the user, not for them. Capture why/what, never how.
  - Commit before Design. This file is the gate.
  - Non-technical contributors can edit via connectors/plugins without touching git directly.
Replace every {{...}} and delete this comment block before committing.
-->

# Intent: {{feature or project name}}

- **Status:** draft | in-review | approved
- **Originator:** {{who}}    **Approver (product owner):** {{who}}
- **Created:** {{YYYY-MM-DD}}    **Approved:** {{YYYY-MM-DD}}

## Problem
{{What hurts today, for whom, and the evidence it matters.}}

## Goals
- {{outcome the change must achieve — observable, not a solution}}

## Non-goals
- {{explicitly out of scope, to bound the agent}}

## Constraints
- **Policy skills to apply:** {{e.g. security, compliance, data-handling — the skills Codex must load in Design}}
- **Sandbox / access:** {{read-only | workspace-write | needs network — what Codex is allowed to touch}}
- **Tech / platform limits:** {{languages, services, latency, budget}}

## Success criteria
- {{measurable acceptance signal — a test, a metric, a demo the approver can verify}}

## Open questions
- [ ] {{unknown blocking Design — assign an owner}}

<!-- On approval: set Status=approved, fill Approved date, commit. Codex then runs stage 2 (Design → spec.md). -->
