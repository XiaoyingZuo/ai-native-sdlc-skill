<!--
TEMPLATE: CLAUDE.md  (SDLC stage 3+ — durable institutional knowledge for Claude)
Claude usage:
  - Claude Code auto-loads CLAUDE.md from the repo root (and nested dirs) as project memory.
  - This is where lasting conventions live — NOT one-off prompts. Update it during Build/Maintain.
  - Keep it terse: only what Claude can't infer from the code itself.
Replace every {{...}} and delete this comment block before committing.
-->

# {{project name}} — project memory

## What this repo is
{{One or two sentences: purpose and the main entry points.}}

## Build / test / run
```bash
{{install}}      # e.g. uv sync  /  npm ci
{{test}}         # e.g. pytest -q  /  npm test   <- run before presenting any change
{{build}}        # e.g. make build
{{run}}          # e.g. python -m app
```

## Conventions
- {{code style, naming, patterns Claude must follow}}
- {{libraries to use / avoid}}

## Permission defaults
- **Default mode:** {{default | plan | acceptEdits}}
- **Ask before:** {{operations that need confirmation — network, deploy, migrations}}
- **Never do without asking:** {{destructive/irreversible actions specific to this repo}}

## Policy skills for this repo
- {{skill name}} — {{when it applies}}

## SDLC artifacts
- Intent/specs live in {{docs/sdlc/ or repo root}}.
- Every feature starts from an approved `intent.md` (see the ai-native-sdlc skill).

## Gotchas
- {{non-obvious traps, flaky areas, required env vars}}
