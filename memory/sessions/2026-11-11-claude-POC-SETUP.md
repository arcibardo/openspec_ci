# Session 2026-11-11 — claude — POC-SETUP

## Done
- Explored repo: Vite/React app, no OpenSpec, no workflows.
- Agreed design with the user (see `docs/PLAN.md` decisions D1–D8).
- Step 1: `openspec init --tools claude` → commit 80603ac.
- Step 2: wrote `docs/PLAN.md`, `docs/PROGRESS.md`, `memory/repo/{environment,ci-gotchas}.md`.

## Skills
- Listed as relevant, not invoked yet: `git-workflow-and-versioning`, `documentation-and-adrs`,
  `ci-cd-and-automation` (steps 6–8), `security-and-hardening` (secret handling in workflows).

## User rules observed
- Every step after the OpenSpec init waits for approval before commit.

## Step 4 (agent runner)
- `claude --help` (2.1.322) checked. Used flags: `-p`, `--output-format json`,
  `--permission-mode dontAsk`, `--allowedTools`, `--model`. No `--max-turns` in this version's help.
- `--bare` NOT used: it limits auth to ANTHROPIC_API_KEY/apiKeyHelper, which breaks subscription
  auth via CLAUDE_CODE_OAUTH_TOKEN.
- Verify verdict is parsed from a final `VERDICT: PASS|FAIL|INCONCLUSIVE` line; missing = inconclusive.
- Local run 1 (`plan task-1`): 21 turns, success, but every Bash call was denied (user `rtk`
  hook + chained commands), so artifacts were hand-written without the CLI. Validated externally: OK.
- Fix: `--setting-sources project` + one-command-per-call rule in prompts.
- Local run 2: 38 turns, model claude-fable-5-1, `openspec validate --strict` ran and passed.
  Only remaining denial: `Skill(opsx:propose)` → added `Skill` to the allowlist.
- Local run 3: 41 turns, zero permission denials, `/opsx:propose` ran via Skill, strict validation passed.
- Both generated changes copied to the session scratchpad and removed from the repo.
- Run 3 change saved (outside the repo, session scratchpad, may be wiped):
  /private/tmp/claude-501/-Users-davide-arcinotti-learn-openspec-ci/3611e8ae-6f27-4778-82c9-693e085de06c/scratchpad/run3-task-1-version-footer
- Committed step 4 as `004`.

## Decisions taken in this session
- OpenSpec skills/commands stay in `.claude/` (Claude Code only reads skills from there; `.github/`
  is for workflows and Copilot files). For Copilot later: `openspec init --tools claude,github-copilot`.

## Handoff
Next session starts at step 5. See `docs/PROGRESS.md` → "Next action".

## Step 5 (openspec-ops.sh) — new session after /clear
- Skills listed as relevant: `ci-cd-and-automation`, `security-and-hardening`,
  `git-workflow-and-versioning`. None were invoked; the conventions were applied directly
  (input validation for task/change ids and manifest keys, exit codes 0/1/2).
- Wrote `scripts/openspec-ops.sh`: `change-id`, `validate`, `tasks-complete`, `guard-plan-diff [base]`,
  `manifest key=value...`. Decision (user-approved): gitignore `.openspec-ci/runs/`, commit only
  `manifest.json`; reports go to PR comments and workflow artifacts.
- Tested against a copy of the run-3 change (`task-1-version-footer`): every pass/fail path behaved
  as expected, including 0/1/2 matches, strict validate, unticked/ticked tasks, a `src/` edit caught
  by the guard, and a manifest merge. The guard's passing case was tested in a temp git worktree. The
  copy was removed afterwards.
- `openspec list --json` shape: `{ "changes": [{ "name": ... }], "root": {...} }`.
- Commit `006` after the user approves.

## Step 6 (openspec-plan.yml)
- Skills used as guidance: `ci-cd-and-automation`, `security-and-hardening` (inputs only via `env:`,
  explicit `permissions:`, secret scoped to the agent step only).
- Wrote `.github/workflows/openspec-plan.yml`: dispatch with `task` → validate input + branch must not
  exist → install pinned openspec 1.14.1 / claude-code 2.1.322 → run-agent plan → guard-plan-diff →
  change-id + validate → manifest → branch `openspec/<task>`, commit, push, `gh pr create --draft`
  (body = agent report + run link) → always upload runs/ artifact + job summary.
- No pnpm in the plan job (no build needed). Checkout uses shallow history (the guard only reads the
  working tree).
- Checked statically only: PyYAML parse plus `bash -n` on every `run:` block, and an assert that no
  `${{ }}` appears inside `run:`. Not run on GitHub yet (step 11).
- Commit `007` after the user approves.
