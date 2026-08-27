---
name: ai-native-sdlc
description: Use when starting or running a feature/project through its full lifecycle and the user wants an AI-native workflow where code is not the bottleneck. Drives work through six artifact-committing stages (Plan→Design→Build→Test→Deploy→Maintain), keeping humans at judgment gates and git as the system of record.
---

# AI-Native SDLC

Reframe the software lifecycle around the fact that **code is no longer the bottleneck**.
Claude handles volume (writing intent, specs, tests, code, reviews); the human stays
accountable for judgment at approval gates. Every stage commits a version-controlled
Markdown artifact — git is the audit trail, and each committed artifact is the gate that
triggers the next stage.

## Core rules

1. **Artifacts are the source of truth.** Never advance a stage without committing its file.
2. **Humans gate, agents fill.** Stop and request explicit approval at each gate below.
   Do not silently proceed from Plan→Design or Design→Build.
3. **Policies live as skills.** Reuse org skills (security, compliance, UX) during Design
   instead of re-deriving constraints each time.
4. **Institutional knowledge → `CLAUDE.md`.** Keep durable project conventions in the
   repo's auto-loaded `CLAUDE.md`, not in one-off prompts.

## The six stages

Stages are modular — adopt in the order the task's constraints allow. Default flow:

### 1. Plan → `intent.md`
Brainstorm with the user to produce a machine-readable, version-controlled requirements
doc. Capture the *why* and *what*, not the *how*. Sections: Problem, Goals, Non-goals,
Constraints, Success criteria, Open questions.
**Gate:** product owner reviews and approves `intent.md` before Design.

### 2. Design → `spec.md`
From the approved `intent.md`, generate requirements + technical design in one session,
constrained by applicable org skills. Sections: Approach, Architecture/interfaces, Data
model, Risks & tradeoffs, Test strategy, Rollout plan.
**Gate:** reviewer approves `spec.md` before Build.

### 3. Build
Implement against `spec.md`. Write tests alongside code. Record any convention decisions
in `CLAUDE.md`. Commit code referencing the spec.

### 4. Test
Weave evaluation through implementation — not a gated QA phase. Run the project's real
test/build commands; fix failures before presenting results. Add coverage for new behavior.

### 5. Deploy
Layered agentic review first; reserve human review for regulated / high-blast-radius code.
Governance is enforced *during* the action (checks in CI / pre-merge), not post-hoc.
**Gate:** human sign-off for regulated code paths.

### 6. Maintain
Monitor production. When a control is breached or a defect surfaces, open a new `intent.md`
entry — closing the loop back to stage 1.

## Artifact templates

Copy the ready-made templates in this skill's `templates/` folder into the repo
(or a `docs/sdlc/` folder) as each stage begins, then fill the `{{...}}` placeholders:

```bash
SKILL_DIR="$HOME/.claude/skills/ai-native-sdlc/templates"
cp "$SKILL_DIR/intent.md" ./intent.md   # Plan
cp "$SKILL_DIR/spec.md"   ./spec.md     # Design
cp "$SKILL_DIR/CLAUDE.md" ./CLAUDE.md   # Build — durable conventions (Claude auto-loads this)
```

Each template is Claude-flavored: `intent.md` names the **policy skills** and **permission
mode** up front; `spec.md` ties requirements → tests/evals and records the **permission mode +
`claude -p` (headless)** deploy path; `CLAUDE.md` is the repo's auto-loaded project memory.

## Operating checklist

- [ ] Which stage are we in? If unclear, ask or infer from existing artifacts.
- [ ] Does the prior stage's artifact exist and is it approved? If not, do that first.
- [ ] At a gate, stop and request human approval — state what you need signed off.
- [ ] On completion, commit the artifact and name the next stage.

## Signals this skill applies

"take this feature end to end", "write the spec", "capture requirements", "set up our
dev workflow", "we need an audit trail", "let AI do the implementation, I'll approve".
