---
name: environment
description: Tool versions, repo layout and auth approach for the OpenSpec CI POC
---

- Repo: `github.com/mastrobardo/openspec_ci_test`. This repo is the POC; work on `main`.
- App: Vite + React 19 + TS, pnpm. Scripts: `build` (tsc -b && vite build), `lint`. No tests.
- Local tools (2026-11-11): openspec 1.14.1, Claude Code 2.1.322, gh 2.94.0, node 22.22.0.
- OpenSpec initialized with `openspec init --tools claude --no-animation --no-copilot-cloud .`,
  schema `spec-driven`. Generates `/opsx:*` commands and `openspec-*` skills in `.claude/`.
- Other `.claude/skills/*` (non-openspec) are the user's own skills, committed by the user in
  953d591. They are available to Claude in CI too.
- CI auth: `claude setup-token` → secret `CLAUDE_CODE_OAUTH_TOKEN` (subscription, not API key).

Related: [[ci-gotchas]]
- OpenSpec skills/commands live in `.claude/` (Claude Code reads only there), not `.github/`.
- A local `plan` run of task-1 took 21–41 turns on claude-fable-5-1 (notional $0.46–1.92 per the
  JSON's `total_cost_usd`; charged to subscription quota).
