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
| 6 | `.github/workflows/openspec-plan.yml` | done | 007 |
| 6b | Plan workflow: `model` dropdown (sonnet/opus/fable), token whitespace check, model + attempt in manifest | done | 008 |
| 7a | `openspec-apply.yml` first apply (`ready_for_review` + dispatch `pr`), `guard-diff`, manifest `model` alias | done (static + local tests); **not yet run on GitHub** (see Next action) | 009 |
| 7b | Re-apply with review feedback (`changes_requested`), `pr-feedback.sh`, `reapply` prompt/stage | done (static + simulated); **not yet run on GitHub** | 010 |
| 8 | `.github/workflows/openspec-verify.yml` | todo | |
| 9 | README: setup + run instructions | todo | |
| 10 | User: push, add secret, change repo settings | done: pushed, secret set, Actions enabled, create/approve PRs allowed, default perms read (checked 2026-11-11) | |
| 11 | E2E run: Plan → draft PR → Ready → Apply → Approve → Verify | plan passed in CI on the old repo (run 43091589972 → PR #1); then blocked by GitHub `startup_failure`; moved to `arcibardo/openspec_ci` | |
| 11b | Move to `arcibardo/openspec_ci` (remote `origin`; old repo = remote `mastrobardo`), secret + PR permission set | done | 011 |

## Next action

**Blocked on GitHub (as of 2026-11-11 ~21:00Z).** Every workflow run fails with `startup_failure` and
no jobs, on both the old repo and the new account/repo. This is a GitHub-side problem: the
byte-identical 007 Plan workflow that succeeded at 19:54Z fails the same way (see `ci-gotchas.md`).
The project moved to `arcibardo/openspec_ci` (see `memory/repo/environment.md`).

1. **Retry one Plan run on the new repo** (user or agent, uses quota if it starts):
   `gh workflow run "OpenSpec Plan" -R arcibardo/openspec_ci -f task=task-1 -f model=sonnet`.
   Check with `gh api 'repos/arcibardo/openspec_ci/actions/runs?per_page=3'` (the list endpoint,
   not the single-run one). If it still fails at startup a day later, contact GitHub Support with run
   ids 43098031317 (old repo, `probe/007`) and 43098756576 (new repo).
2. When Plan works: it opens draft PR #1 **on the new repo** (the old PR #1 stays on the old repo,
   abandoned). Then: mark it Ready → Apply runs. The PR is created after the workflows exist, so the
   merge ref is not stale; keep dispatch `-f pr=N` as the fallback.
3. Test re-apply: "Request changes" with comments (an inline nit + a requirement such as "also show
   the git SHA"). Expected: a re-apply comment with a Spec impact box, follow-up tasks, and specs
   changed only for requirement items.
4. **Step 8: Verify** (plan to be shown first): `pull_request_review` approved (+ dispatch `pr`), model
   from the manifest, `tasks-complete` + `validate` + `run-agent.sh verify` (read-only), report as a PR
   comment, exit 3/4 = failed check, concurrency group shared with Apply per PR. Open question: also
   run on `synchronize` so it can be a required check?

Leftovers on the old repo (`mastrobardo/openspec_ci_test`): branch `probe/007`, PR #1, and
uncancellable `startup_failure` runs. Deleting `probe/007` needs the `mastrobardo` gh account (the
user decides).

## Facts for the next session

- Agreed decisions D9–D11 are in `docs/PLAN.md` (single model choice; re-apply trigger; spec changes
  through `/opsx:update` + Spec impact table).
- Manifest keys: `task change stage status model plan_model plan_run_id plan_run_attempt base_sha
  apply_run_id apply_run_attempt applied_at apply_count created_at updated_at`.
  `stage` is `plan`, `apply` or `reapply`; `applied_at` = start of the last (re)apply run and the
  cutoff for the next feedback collection.
  `model` = the alias picked at Plan, read by every later stage.
- `openspec-ops.sh` contract: exit 0 ok, 1 usage/setup error, 2 check failed.
  `guard-diff <apply|reapply> <change> <base-sha>` compares the working tree and untracked files
  against base-sha.
- Actions: checkout/setup-node/upload-artifact `@v7`, pnpm/action-setup `@v6` (pnpm 10); openspec
  `1.14.1`, claude-code `2.1.322`. Workflows are validated with PyYAML, `bash -n` on each `run:`,
  no `${{ }}` inside `run:`, and step-order checks for `steps.X` refs. actionlint and shellcheck
  are not installed.
- `scripts/pr-feedback.sh <pr> <since|-> <out>` prints the item count. `PR_FEEDBACK_INPUT=<json>`
  is a test hook that skips the API. The workflow writes feedback to `.openspec-ci/runs/feedback.md`
  (gitignored) and the guard's spec impact to `.openspec-ci/runs/spec-impact.txt`.
- PR #1 was authored by `github-actions`, so the owner can Approve or Request changes on it.
- Conventions: commits are numbered `NNN:` (`memory/repo/conventions.md`); every step waits for
  user approval before commit; re-confirm whenever the plan changes.

## Open questions

- Should Verify also run on `pull_request: synchronize` so it can be a required check? (step 8)
- Should archival be added after a passing verify? (out of scope for now)
