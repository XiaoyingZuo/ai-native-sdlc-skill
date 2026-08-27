<!--
TEMPLATE: intent.md  (SDLC stage 1 — Plan)
Claude usage:
  - Fill this WITH the user, not for them. Capture why/what, never how.
  - Commit before Design. This file is the gate.
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
- **Policy skills to apply:** {{e.g. security, compliance, data-handling — the skills Claude must load in Design}}
- **Permission mode:** {{default | plan | acceptEdits | bypassPermissions — how much Claude may act unattended}}
- **Tech / platform limits:** {{languages, services, latency, budget}}

## Success criteria
- {{measurable acceptance signal — a test, a metric, a demo the approver can verify}}

## Open questions
- [ ] {{unknown blocking Design — assign an owner}}

<!-- On approval: set Status=approved, fill Approved date, commit. Claude then runs stage 2 (Design → spec.md). -->
