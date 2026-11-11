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
