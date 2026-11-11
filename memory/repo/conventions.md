---
name: conventions
description: Commit numbering and approval workflow the user wants for this POC
---

- Commit messages start with an incremental 3-digit number: `001: ...`, `002: ...`, `003: ...`.
  Check `git log --oneline` for the last number before committing.
- Every change waits for explicit user approval before it is committed (OpenSpec init was the only
  exception). The user sometimes commits themselves.
- Sample tasks live in `tasks/task-N.txt`, each a minimal-impact change with acceptance criteria and
  an out-of-scope list.

Related: [[environment]]
