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
| 5 | `scripts/openspec-ops.sh` (validate, tasks-complete check, change-id lookup, manifest) | todo | |
| 6 | `.github/workflows/openspec-plan.yml` | todo | |
| 7 | `.github/workflows/openspec-apply.yml` | todo | |
| 8 | `.github/workflows/openspec-verify.yml` | todo | |
| 9 | README: setup + run instructions | todo | |
| 10 | User: push, add secret, change repo settings | done: pushed, secret set, Actions enabled, create/approve PRs allowed, default perms read (checked 2026-11-11) | |
| 11 | E2E run: Plan → draft PR → Ready → Apply → Approve → Verify | todo | |

## Next action

**Step 5: `scripts/openspec-ops.sh`**. Write it, show it to the user, and commit as `006` only on approval.
Intended subcommands (the workflows call these; no agent logic inside):
- `change-id <task-id>`: find the single change in `openspec/changes/` named `<task-id>-*`
  (`openspec list --json`); fail if there are none or several.
- `validate <change-id>`: `openspec validate <change-id> --strict`.
- `tasks-complete <change-id>`: fail if `tasks.md` has any unticked `- [ ]`.
- `guard-plan-diff`: fail if the working-tree diff touches anything outside `openspec/` (enforces
  "planning only" independently of the agent's tool allowlist).
- `manifest <key=value>...`: create/update `.openspec-ci/manifest.json` (task, change, stage,
  status, run id, sha, timestamps).
Also decide: gitignore `.openspec-ci/runs/` (local run output) vs commit reports on the PR branch.

How to test without spending quota: the run-3 change is saved outside the repo (see session log);
copy it back into `openspec/changes/` to exercise the subcommands, then remove it.

Not yet tested with a real agent: the `apply` and `verify` stages (only `plan` ran locally).
Conventions: commits are numbered `NNN:` (`memory/repo/conventions.md`); every step waits for
user approval before commit.

## Open questions

- Should Verify also run on `pull_request: synchronize` so it can be a required check? (step 8)
- Should archival be added after a passing verify? (out of scope for now)
