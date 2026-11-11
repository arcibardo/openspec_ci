# Progress map

Resume rule: read `docs/PLAN.md`, this file, and `memory/repo/*.md`, then continue from the first
unchecked step. **Every step needs explicit user approval before it is committed.**

| # | Step | Status | Commit |
|---|------|--------|--------|
| 1 | `openspec init --tools claude` (openspec/, .claude/commands/opsx, .claude/skills/openspec-*) | done | 80603ac |
| 2 | Plan, progress map, repo memory, session log | done | 953d591 (user) |
| 3 | `tasks/task-1.txt` sample task | done | 002 (4316fed) |
| 3b | `tasks/task-{2,3,4}.txt` (heading text, counter reset, tab title) | done | 003 (8ed97f3) |
| 4 | `scripts/run-agent.sh` + `scripts/prompts/{plan,apply,verify}.md` (plan stage verified locally) | done | 004 |
| 5 | `scripts/openspec-ops.sh` (change-id, validate, tasks-complete, guard-plan-diff, manifest); `.openspec-ci/runs/` gitignored | done | 006 |
| 6 | `.github/workflows/openspec-plan.yml` | todo | |
| 7 | `.github/workflows/openspec-apply.yml` | todo | |
| 8 | `.github/workflows/openspec-verify.yml` | todo | |
| 9 | README: setup + run instructions | todo | |
| 10 | User: push, add secret, change repo settings | done: pushed, secret set, Actions enabled, create/approve PRs allowed, default perms read (checked 2026-11-11) | |
| 11 | E2E run: Plan → draft PR → Ready → Apply → Approve → Verify | todo | |

## Next action

**Step 6: `.github/workflows/openspec-plan.yml`**. Write it, show it to the user, and commit as `007`
only on approval. Outline (`workflow_dispatch`, input `task`):
- `permissions: contents: write, pull-requests: write` (repo default is read).
- Setup: checkout, pnpm + node 22, `npm i -g @anthropic-ai/claude-code @fission-ai/openspec` (check
  the openspec package name before use), `jq`.
- `scripts/run-agent.sh plan <task>` with `CLAUDE_CODE_OAUTH_TOKEN` from secrets.
- `scripts/openspec-ops.sh guard-plan-diff`, `change-id <task>`, `validate <change>`.
- `manifest task=… change=… stage=plan status=planned run_id=$GITHUB_RUN_ID sha=…`.
- Branch `openspec/<task>`, commit `openspec/` + `.openspec-ci/manifest.json`, `gh pr create --draft`
  with `.openspec-ci/runs/plan.md` as the body. Upload `.openspec-ci/runs/` as a workflow artifact.

`openspec-ops.sh` contract: exit 0 ok, 1 usage/setup error, 2 check failed. Raw agent output in
`.openspec-ci/runs/` is gitignored; only `manifest.json` is committed on PR branches.

Not yet tested with a real agent: the `apply` and `verify` stages (only `plan` ran locally).
Conventions: commits are numbered `NNN:` (`memory/repo/conventions.md`); every step waits for
user approval before commit.

## Open questions

- Should Verify also run on `pull_request: synchronize` so it can be a required check? (step 8)
- Should archival be added after a passing verify? (out of scope for now)
