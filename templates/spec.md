<!--
TEMPLATE: spec.md  (SDLC stage 2 — Design)
Codex usage:
  - Generate from an APPROVED intent.md in a single session. Load the policy skills named in intent.md.
  - Requirements + technical design live together here — no separate analyst/designer handoff.
  - Commit before Build. This file is the gate and the contract the implementation is checked against.
Replace every {{...}} and delete this comment block before committing.
-->

# Spec: {{feature or project name}}

- **Status:** draft | in-review | approved
- **Intent:** ./intent.md ({{intent status/commit}})
- **Author:** Codex    **Reviewer:** {{who}}    **Approved:** {{YYYY-MM-DD}}
- **Skills applied:** {{list the policy skills loaded, e.g. security, compliance}}

## Requirements
Traceable to intent goals.
- **R1** — {{requirement}}  → covers intent goal {{#}}
- **R2** — {{requirement}}

## Approach
{{Chosen strategy in a few sentences, and why over alternatives.}}

## Architecture & interfaces
- **Components:** {{modules/services touched or added}}
- **Public interfaces:** {{APIs, CLI commands, function signatures}}
- **MCP / tools:** {{any MCP servers or external tools Codex needs at build/run time}}

## Data model
{{New/changed schemas, migrations, storage. "N/A" if none.}}

## Risks & tradeoffs
- {{risk}} → **mitigation:** {{...}}

## Test strategy
- **Commands:** {{exact test/build commands — e.g. `pytest -q`, `npm test`}}
- **New coverage:** {{behaviors that need tests, mapped to R1/R2...}}
- **Evals (if model/agent work):** {{benchmark + metric, e.g. SpreadsheetBench pass@1}}

## Deploy & governance
- **Execution:** {{how it ships — CI job, `codex exec` in pipeline, manual}}
- **Sandbox / approval mode at deploy:** {{read-only | workspace-write | on-request}}
- **Human sign-off required?** {{yes if regulated/high-blast-radius, else agentic review only}}
- **Rollout / rollback:** {{flag, staged %, revert plan}}

## Definition of done
- [ ] All requirements implemented and traced
- [ ] Test commands pass; new coverage added
- [ ] Conventions recorded in AGENTS.md
- [ ] Artifacts committed

<!-- On approval: set Status=approved, commit. Codex then runs stage 3 (Build). -->
