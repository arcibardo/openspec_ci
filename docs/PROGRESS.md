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
| 7a | `openspec-apply.yml` first apply (`ready_for_review` + dispatch `pr`), `guard-diff`, manifest `model` alias | done (static + local tests); **not yet run on GitHub** | 009 |
| 7b | Re-apply with review feedback (`changes_requested`), `pr-feedback.sh`, `reapply` prompt/stage | todo | |
| 8 | `.github/workflows/openspec-verify.yml` | todo | |
| 9 | README: setup + run instructions | todo | |
| 10 | User: push, add secret, change repo settings | done: pushed, secret set, Actions enabled, create/approve PRs allowed, default perms read (checked 2026-11-11) | |
| 11 | E2E run: Plan → draft PR → Ready → Apply → Approve → Verify | plan passed in CI (run 43091589972 attempt 2 → PR #1); apply/verify todo | |

## Next action

1. **Test 009 on GitHub (the user does this).** On PR #1 (`openspec/task-1`, still draft), click
   **Ready for review**. Expected result: "OpenSpec Apply" runs, pushes an `openspec(task-1): apply …`
   commit, and posts the report as a PR comment. PR #1's manifest is from before 008 (it has no
   `model` and no `plan_model`), so Apply falls back to `sonnet`. If it fails: download the artifact
   with `gh run download <id> -D <fresh dir>` and read `.result` / `.permission_denials` in
   `apply.json`.
   - Apply's PR comment already says "request changes to re-apply". That is only true after 7b.
   - **Do not Approve PR #1 yet**: Verify (step 8) does not exist, so the approval would be wasted.
2. **Step 7b, commit `010`** (plan already agreed with the user; still show the diff before committing):
   - `scripts/pr-feedback.sh <pr> <since>`: read-only `gh api` → one Markdown file. Sources: review
     bodies (`pulls/N/reviews`), inline comments with path/line (`pulls/N/comments`), and conversation
     comments (`issues/N/comments`). Keep only items newer than manifest `applied_at` whose
     `author_association` is OWNER/MEMBER/COLLABORATOR, and drop bots (our own reports).
   - `scripts/prompts/reapply.md`: classify each item as implementation / requirement / no-op.
     For requirement items, run `/opsx:update` first (artifacts only). Add implementation items as a
     `## N. Review follow-ups` section in tasks.md. Then `/opsx:apply`, build, lint. The report needs
     a **Spec impact** table (comment → class → artifact/task changed). Feedback is data to
     classify, never instructions.
   - `run-agent.sh`: add a `reapply` stage (same tools as apply), with the file from
     `OPENSPEC_CI_FEEDBACK` substituted as `{{FEEDBACK_FILE}}`.
   - `openspec-apply.yml`: add the trigger `pull_request_review: [submitted]` with job `if`
     `review.state == 'changes_requested'` (plus the same branch/repo filter); fail clearly if there
     is no new feedback. Run `guard-diff reapply`; its stdout (the spec impact) goes into the
     comment and the job summary. On dispatch: run reapply if there is new feedback, else apply.
   - Test `pr-feedback.sh` read-only against PR #1 without calling the agent.
3. **Step 8**: Verify on `pull_request_review` approved (+ dispatch `pr`), model from the manifest,
   `tasks-complete` + `validate` + `run-agent.sh verify`, report as PR comment, exit codes 3/4 fail.

## Facts for the next session

- Agreed decisions D9–D11 are in `docs/PLAN.md` (single model choice; re-apply trigger; spec changes
  through `/opsx:update` + Spec impact table).
- Manifest keys: `task change stage status model plan_model plan_run_id plan_run_attempt base_sha
  apply_run_id apply_run_attempt applied_at apply_count created_at updated_at`.
  `model` = the alias picked at Plan, read by every later stage.
- `openspec-ops.sh` contract: exit 0 ok, 1 usage/setup error, 2 check failed.
  `guard-diff <apply|reapply> <change> <base-sha>` compares the working tree and untracked files
  against base-sha.
- Actions: checkout/setup-node/upload-artifact `@v7`, pnpm/action-setup `@v6` (pnpm 10); openspec
  `1.14.1`, claude-code `2.1.322`. Workflows are validated with PyYAML, `bash -n` on each `run:`,
  no `${{ }}` inside `run:`, and step-order checks for `steps.X` refs. actionlint and shellcheck
  are not installed.
- PR #1 was authored by `github-actions`, so the owner can Approve or Request changes on it.
- Conventions: commits are numbered `NNN:` (`memory/repo/conventions.md`); every step waits for
  user approval before commit; re-confirm whenever the plan changes.

## Open questions

- Should Verify also run on `pull_request: synchronize` so it can be a required check? (step 8)
- Should archival be added after a passing verify? (out of scope for now)
