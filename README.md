# AI-Native SDLC — a Codex & Claude Code Skill

A skill that runs a feature or project through its full software development lifecycle in an
**AI-native** way: the agent handles the volume (intent, specs, tests, code, reviews) while
humans stay accountable at judgment gates, and **git is the audit trail**.

Ships in **two flavors** — one for [Codex](https://developers.openai.com/codex), one for
[Claude Code](https://docs.claude.com/en/docs/claude-code) — with identical stages and rules,
adapted to each harness's native mechanisms.

> 中文版:[README.zh-CN.md](./README.zh-CN.md)

## Where this came from

This skill is an adaptation of Anthropic's blog post
[**The AI-Native SDLC Playbook**](https://claude.com/blog/the-ai-native-sdlc-playbook).

The blog's core thesis: **code is no longer the bottleneck.** Traditional SDLC processes were
designed when writing code consumed most of a project's time. Now that agents implement
quickly, the real constraints have shifted to human-speed stages — review, approval, deploy.
So the workflow is rebuilt around **version-controlled Markdown artifacts**: every stage
commits a file, that file is the gate for the next stage, and git becomes the system of record.

## Repository layout

```
codex/                 # Codex flavor
  SKILL.md
  templates/{intent.md, spec.md, AGENTS.md}
claude/                # Claude Code flavor
  SKILL.md
  templates/{intent.md, spec.md, CLAUDE.md}
```

Both flavors define the same six stages and core rules. Only the harness-native details differ:

| Concept | Codex | Claude Code |
|---|---|---|
| Auto-loaded project memory | `AGENTS.md` | `CLAUDE.md` |
| Unattended-action control | sandbox mode (`read-only` / `workspace-write`) | permission mode (`default` / `plan` / `acceptEdits`) |
| Headless / pipeline run | `codex exec` | `claude -p` |
| Skill location | `~/.codex/skills/` | `~/.claude/skills/` |
| Policy-as-skill | yes | yes |

## The six stages

| # | Stage | Artifact | Human gate |
|---|-------|----------|------------|
| 1 | Plan     | `intent.md` | Product owner approves before Design |
| 2 | Design   | `spec.md`   | Reviewer approves before Build |
| 3 | Build    | code + tests, conventions → `AGENTS.md`/`CLAUDE.md` | — |
| 4 | Test     | woven through Build, not a gated phase | — |
| 5 | Deploy   | layered agentic review | Human sign-off for regulated code |
| 6 | Maintain | breached control → new `intent.md` entry | closes the loop back to Plan |

Core rules: artifacts are the source of truth; humans gate, agents fill; policies live as
reusable skills; institutional knowledge goes in the auto-loaded memory file (not one-off prompts).

## Install

**Codex:**
```bash
git clone https://github.com/XiaoyingZuo/ai-native-sdlc-skill.git /tmp/ansdlc
cp -R /tmp/ansdlc/codex "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc"
```

**Claude Code:**
```bash
git clone https://github.com/XiaoyingZuo/ai-native-sdlc-skill.git /tmp/ansdlc
cp -R /tmp/ansdlc/claude "$HOME/.claude/skills/ai-native-sdlc"
```

Both harnesses load a skill by its `SKILL.md` frontmatter (`name` + `description`) and trigger
it automatically when your request matches — no manual registration needed.

## Usage tutorial

**1. Start a piece of work.** Tell the agent what you want, e.g.
*"take this feature end to end using our AI-native SDLC"*. It loads the skill and asks which
stage you're in.

**2. Plan — produce `intent.md`.** Copy the template and fill it *with* the agent:

```bash
# Codex:  cp "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc/templates/intent.md" ./intent.md
# Claude: cp "$HOME/.claude/skills/ai-native-sdlc/templates/intent.md" ./intent.md
```

Capture the *why/what* (Problem, Goals, Non-goals, Constraints, Success criteria). In
Constraints, name the **policy skills** and the **sandbox/permission mode** the agent should
use. Commit when the product owner approves — that commit is the gate.

**3. Design — produce `spec.md`.** From the approved intent, the agent generates requirements +
design in one session. Requirements are numbered (R1, R2…) and traced back to intent goals; the
Test strategy names exact commands (and evals if it's model work); Deploy records the mode and
the headless run path (`codex exec` / `claude -p`). Commit when approved.

**4. Build + Test.** The agent implements against the spec, writes tests alongside code, and
records lasting conventions in `AGENTS.md`/`CLAUDE.md` (auto-loaded next time). Run the real
test/build commands before presenting changes.

**5. Deploy + Maintain.** Agentic review first; human sign-off only for regulated / high-blast
paths. In production, a breached control or new defect opens a fresh `intent.md` entry —
closing the loop.

## Example: a benchmark-improvement feature

Goal: raise pass@1 on a spreadsheet benchmark by adding a new prompting strategy.

1. **`intent.md`** — Problem: current pass@1 plateaued. Goal: +X points on the eval set.
   Non-goal: no changes to the harness. Constraints: `workspace-write` (Codex) /
   `acceptEdits` (Claude), apply the `eval-safety` policy skill. Success criteria: eval
   pass@1 ≥ target on the held-out split.
2. **`spec.md`** — R1: add strategy module; R2: wire it behind a flag. Test strategy:
   `pytest -q` + run the benchmark eval, metric = pass@1. Deploy: headless run in the eval CI,
   agentic review only (not regulated).
3. **Build/Test** — the agent implements the module, adds unit tests, runs the eval, records
   the flag convention in the memory file.
4. **Deploy/Maintain** — merged behind the flag after the eval clears target; if a later run
   regresses, that regression opens a new `intent.md`.

The payoff: at every step there's a committed, reviewable artifact — a full audit trail from
intent to production, with humans spending attention on approvals instead of implementation.

## License

Use freely. Adapted from Anthropic's *The AI-Native SDLC Playbook* concept for Codex & Claude Code.
