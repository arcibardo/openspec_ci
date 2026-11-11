---
name: ci-gotchas
description: GitHub Actions trigger/permission pitfalls relevant to the OpenSpec lifecycle
---

- Pushes and PRs created with `GITHUB_TOKEN` do not trigger other workflows. The lifecycle relies on
  human actions (Ready for review, Approve) as triggers, so this is fine.
- `gh pr create` from Actions needs "Allow GitHub Actions to create and approve pull requests"
  plus `pull-requests: write` permission.
- A check from `pull_request_review` is reported separately from `pull_request` checks; branch
  protection may need a `pull_request` trigger too.
- `claude -p` in CI cannot prompt for permissions: tool allowances must be passed as flags.
  Verify the flags with `claude --help`; never invent them.

Related: [[environment]]

Learned from local plan runs (2026-11-11):
- Locally, user-level hooks (e.g. an `rtk` command rewriter) and global CLAUDE.md leak into
  `claude -p`. `--setting-sources project` drops user settings/hooks; global CLAUDE.md still loads
  (only `--bare` removes it, but `--bare` disables subscription auth). CI has neither.
- `Bash(openspec:*)` does not match chained commands (`a && b`, `a; b`). Prompts tell the agent to run
  one command per call.
- `/opsx:*` slash commands run through the `Skill` tool, which must be in `--allowedTools`.
- Check `.permission_denials` in the `--output-format json` result after each run.
- Repo state checked 2026-11-11 via `gh api repos/mastrobardo/openspec_ci_test/actions/permissions/workflow`:
  Actions enabled, `default_workflow_permissions: read`, `can_approve_pull_request_reviews: true`.
  Workflows must therefore declare explicit `permissions:` (contents/pull-requests: write).

Learned from the first CI plan run (2026-11-11, run 43091589972):
- A `CLAUDE_CODE_OAUTH_TOKEN` pasted from `claude setup-token` can carry the terminal's line wrap.
  Symptom: `turns=1 cost_usd=0 is_error=true`; the cause is in `.result` of `plan.json`
  ("contains a line break"). Fix: `pbpaste | tr -d ' \r\n' | gh secret set CLAUDE_CODE_OAUTH_TOKEN`.
  `run-agent.sh` now rejects whitespace in the token before calling the agent.
- With no `--model`, CI used `claude-sonnet-5-5` (local runs used `claude-fable-5-1`). Workflows
  therefore take a `model` dropdown (`sonnet`/`opus`/`fable` aliases; default `sonnet`).
- Re-running a failed run keeps the run id, and both attempts upload an artifact with the same name.
  `gh run download <id>` then fails with "file exists" unless each attempt goes into a fresh
  directory. The manifest records `plan_run_attempt`.
