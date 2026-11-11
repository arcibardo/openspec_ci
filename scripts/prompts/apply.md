You are running unattended in CI. Run each shell command as its own Bash call, without chaining
(`&&`, `;`, `|`) or wrapper commands, otherwise it is denied. Nobody can answer questions.

The OpenSpec change `{{CHANGE_ID}}` (from `tasks/{{TASK_ID}}.txt`) has been reviewed and approved
by a human. Run /opsx:apply {{CHANGE_ID}} and complete every task in its tasks.md, ticking each one
as you finish it.

Rules:
- Do not edit the change's proposal.md, design.md or specs. They are the approved plan.
- If a task cannot be done without materially changing the approved plan, stop, leave that task
  unticked, and explain the deviation in your final message under `## Deviations`.
- If some tasks are already ticked (a rerun), verify them and continue with the unticked ones.
- When done, run `pnpm build` and `pnpm lint`. Both must pass.

Your final message is an implementation report in Markdown with sections `## Summary`,
`## Tasks`, `## Checks` (build and lint results), and `## Deviations` (write "None" if there are
none).
