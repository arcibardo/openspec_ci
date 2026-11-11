# Progress map

Resume rule: read `docs/PLAN.md`, this file, and `memory/repo/*.md`, then continue from the first
unchecked step. **Every step needs explicit user approval before it is committed.**

| # | Step | Status | Commit |
|---|------|--------|--------|
| 1 | `openspec init --tools claude` (openspec/, .claude/commands/opsx, .claude/skills/openspec-*) | done | 80603ac |
| 2 | Plan, progress map, repo memory, session log | awaiting approval | |
| 3 | `tasks/task-1.txt` sample task | todo | |
| 4 | `scripts/run-agent.sh` + `scripts/prompts/{plan,apply,verify}.md` | todo | |
| 5 | `scripts/openspec-ops.sh` (validate, tasks-complete check, change-id lookup, manifest) | todo | |
| 6 | `.github/workflows/openspec-plan.yml` | todo | |
| 7 | `.github/workflows/openspec-apply.yml` | todo | |
| 8 | `.github/workflows/openspec-verify.yml` | todo | |
| 9 | README: setup + run instructions | todo | |
| 10 | User: push, add secret, change repo settings | todo (user) | |
| 11 | E2E run: Plan → draft PR → Ready → Apply → Approve → Verify | todo | |

## Next action

Step 2: wait for the user to approve the docs/memory files, then commit.

## Open questions

- Should Verify also run on `pull_request: synchronize` so it can be a required check? (step 8)
- Should archival be added after a passing verify? (out of scope for now)
