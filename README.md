# AI-Native SDLC — a Codex Skill

A [Codex](https://developers.openai.com/codex) skill that runs a feature or project through
its full software development lifecycle in an **AI-native** way: the agent handles the volume
(intent, specs, tests, code, reviews) while humans stay accountable at judgment gates, and
**git is the audit trail**.

> Read this in Chinese: [README.zh-CN.md](./README.zh-CN.md)

## Where this came from

This skill is a Codex adaptation of Anthropic's blog post
[**The AI-Native SDLC Playbook**](https://claude.com/blog/the-ai-native-sdlc-playbook).

The blog's core thesis: **code is no longer the bottleneck.** Traditional SDLC processes were
designed when writing code consumed most of a project's time. Now that agents implement
quickly, the real constraints have shifted to human-speed stages — review, approval, deploy.
So the workflow is rebuilt around **version-controlled Markdown artifacts**: every stage
commits a file, that file is the gate for the next stage, and git becomes the system of record.

This repo ports those ideas onto Codex-native mechanisms:
`AGENTS.md` (auto-loaded project context), **policy skills**, and Codex's **sandbox / approval
modes** and `codex exec`.

## What's inside

```
SKILL.md               # the skill Codex loads — rules + the six stages
templates/
  intent.md            # Stage 1 (Plan)   — machine-readable requirements
  spec.md              # Stage 2 (Design) — requirements + technical design
  AGENTS.md            # Stage 3+ (Build) — durable, auto-loaded project conventions
```

## The six stages

| # | Stage | Artifact | Human gate |
|---|-------|----------|------------|
| 1 | Plan     | `intent.md` | Product owner approves before Design |
| 2 | Design   | `spec.md`   | Reviewer approves before Build |
| 3 | Build    | code + tests, conventions → `AGENTS.md` | — |
| 4 | Test     | woven through Build, not a gated phase | — |
| 5 | Deploy   | layered agentic review | Human sign-off for regulated code |
| 6 | Maintain | breached control → new `intent.md` entry | closes the loop back to Plan |

Core rules: artifacts are the source of truth; humans gate, agents fill; policies live as
reusable skills; institutional knowledge goes in `AGENTS.md` (not one-off prompts).

## Install

Clone into your Codex skills directory so Codex auto-discovers it:

```bash
git clone https://github.com/XiaoyingZuo/ai-native-sdlc-skill.git \
  "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc"
```

Codex loads a skill by its `SKILL.md` frontmatter (`name` + `description`) and triggers it
automatically when your request matches — no manual registration needed.

## Usage tutorial

**1. Start a piece of work.** Tell Codex what you want, e.g.
*"take this feature end to end using our AI-native SDLC"*. Codex loads the skill and asks which
stage you're in.

**2. Plan — produce `intent.md`.** Copy the template and fill it *with* Codex:

```bash
cp "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc/templates/intent.md" ./intent.md
```

Capture the *why/what* (Problem, Goals, Non-goals, Constraints, Success criteria). In
Constraints, name the **policy skills** and **sandbox mode** Codex should use. Commit when the
product owner approves — that commit is the gate.

**3. Design — produce `spec.md`.** From the approved intent, Codex generates requirements +
design in one session:

```bash
cp "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc/templates/spec.md" ./spec.md
```

Requirements are numbered (R1, R2…) and traced back to intent goals; the Test strategy names
exact commands (and evals if it's model work); Deploy records the approval mode and `codex exec`
path. Commit when approved.

**4. Build + Test.** Codex implements against the spec, writes tests alongside code, and records
any lasting conventions in `AGENTS.md` (which Codex auto-loads next time). Run the real
test/build commands before presenting changes.

**5. Deploy + Maintain.** Agentic review first; human sign-off only for regulated / high-blast
paths. In production, a breached control or new defect opens a fresh `intent.md` entry —
closing the loop.

## Example: a benchmark-improvement feature

Goal: raise pass@1 on a spreadsheet benchmark by adding a new prompting strategy.

1. **`intent.md`** — Problem: current pass@1 plateaued. Goal: +X points on the eval set.
   Non-goal: no changes to the harness. Constraints: sandbox `workspace-write`, apply the
   `eval-safety` policy skill. Success criteria: eval pass@1 ≥ target on the held-out split.
2. **`spec.md`** — R1: add strategy module; R2: wire it behind a flag. Test strategy:
   `pytest -q` + run the benchmark eval, metric = pass@1. Deploy: `codex exec` in the eval CI,
   agentic review only (not regulated).
3. **Build/Test** — Codex implements the module, adds unit tests, runs the eval, records the
   flag convention in `AGENTS.md`.
4. **Deploy/Maintain** — merged behind the flag after the eval clears target; if a later run
   regresses, that regression opens a new `intent.md`.

The payoff: at every step there's a committed, reviewable artifact — a full audit trail from
intent to production, with humans spending attention on approvals instead of implementation.

## License

Use freely. Adapted from Anthropic's *The AI-Native SDLC Playbook* concept for Codex.
