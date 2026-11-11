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
| 6 | `.github/workflows/openspec-plan.yml` (static checks only; first real run is step 11) | done | 007 |
| 7 | `.github/workflows/openspec-apply.yml` | todo | |
| 8 | `.github/workflows/openspec-verify.yml` | todo | |
| 9 | README: setup + run instructions | todo | |
| 10 | User: push, add secret, change repo settings | done: pushed, secret set, Actions enabled, create/approve PRs allowed, default perms read (checked 2026-11-11) | |
| 11 | E2E run: Plan → draft PR → Ready → Apply → Approve → Verify | todo | |

## Next action

**Step 7: `.github/workflows/openspec-apply.yml`**. Write it, show it to the user, and commit as `008`
only on approval. Outline (`pull_request: types [ready_for_review]`, only for `openspec/task-*` heads):
- `permissions: contents: write, pull-requests: write`; checkout the PR head branch (not the merge ref).
- Read task/change from `.openspec-ci/manifest.json`; `validate` before running the agent.
- pnpm + node 22 + `pnpm install --frozen-lockfile`; same pinned openspec/claude installs as plan.
- `scripts/run-agent.sh apply <task> <change>`, then `tasks-complete`, `validate`, `pnpm build`, `pnpm lint`.
- `manifest stage=apply status=applied apply_run_id=… head_sha=…`, commit + push to the PR branch,
  PR comment with `.openspec-ci/runs/apply.md`; upload runs/ as artifact; job summary.
- Reuse the plan workflow's patterns: inputs via `env:` only, `if: always()` artifact + summary.

Plan workflow facts: actions pinned to `@v7` (checkout, setup-node, upload-artifact; latest
majors on 2026-11-11), openspec `1.14.1`, claude-code `2.1.322`. Validated with PyYAML + `bash -n`
per `run:` block (no actionlint/shellcheck installed).

`openspec-ops.sh` contract: exit 0 ok, 1 usage/setup error, 2 check failed. Raw agent output in
`.openspec-ci/runs/` is gitignored; only `manifest.json` is committed on PR branches.

Not yet tested with a real agent: the `apply` and `verify` stages (only `plan` ran locally).
Conventions: commits are numbered `NNN:` (`memory/repo/conventions.md`); every step waits for
user approval before commit.

## Open questions

- Should Verify also run on `pull_request: synchronize` so it can be a required check? (step 8)
- Should archival be added after a passing verify? (out of scope for now)
