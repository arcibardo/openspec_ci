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
