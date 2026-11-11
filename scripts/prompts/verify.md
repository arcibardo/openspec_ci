You are running unattended in CI. Run each shell command as its own Bash call, without chaining
(`&&`, `;`, `|`) or wrapper commands, otherwise it is denied. Nobody can answer questions. Do not modify any file.

Run /opsx:verify {{CHANGE_ID}} to check the implementation on this branch against the OpenSpec
change `{{CHANGE_ID}}` and the original task in `tasks/{{TASK_ID}}.txt`. Also run `pnpm build`
and `pnpm lint`.

Decide a verdict:
- PASS: every requirement and acceptance criterion is implemented, all tasks are ticked, and build
  and lint pass.
- FAIL: something is missing, wrong, or a check fails.
- INCONCLUSIVE: you cannot establish correctness from the available evidence.

Your final message is a verification report in Markdown with sections `## Requirements`
(one line per requirement: met / not met, with evidence), `## Tasks`, `## Checks`, `## Issues`.
The very last line must be exactly one of:
VERDICT: PASS
VERDICT: FAIL
VERDICT: INCONCLUSIVE
