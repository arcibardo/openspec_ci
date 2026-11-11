You are running unattended in CI. Run each shell command as its own Bash call, without chaining
(`&&`, `;`, `|`) or wrapper commands, otherwise it is denied. Nobody can answer questions: when something is unclear, make the
most conservative assumption and record it.

Read the task in `tasks/{{TASK_ID}}.txt`.

1. Explore the repository to understand what the task affects. Check existing specs with
   `openspec list --specs --json` and existing changes with `openspec list --json`.
2. Run /opsx:propose for this task. The change name MUST start with `{{TASK_ID}}-` followed by a
   short kebab-case slug (example: `{{TASK_ID}}-version-footer`).
3. Run `openspec validate <change-name> --strict` and fix the artifacts until it passes.

Rules:
- Planning only. Create or edit files under `openspec/` only. Do not change application code,
  configuration, or anything else.
- Each acceptance criterion from the task must map to at least one requirement scenario and one
  task in tasks.md.

Your final message is the draft PR body. Write it in Markdown with these sections:
`## Change` (the change name on its own line, formatted as `change: <name>`), `## Exploration`,
`## Proposed changes`, `## Tasks`, `## Assumptions and open questions`, `## Validation`.
