# Implementer Checklist

- Final prompt is present.
- Scope and stop conditions are clear.
- Unrelated worktree changes are preserved.
- No unrequested dependencies are added.
- Existing patterns are reused.
- New productive files are checked as <= 500 lines.
- Productive files already > 500 lines are checked for unauthorized growth.
- `git diff --numstat` is run.
- Untracked files are handled with `git add -N` or an equivalent documented
  method if direct numstat does not see them.
- `numstat_method` is reported as direct, intent_to_add, or equivalent.
- Added + deleted lines are calculated.
- R2 is evaluated as added + deleted.
- R2 status is reported.
- Diff size is measured.
- Required checks are run or marked skipped.
- Changed files are reported.
- Risks and unknowns are reported.
- Operator response is short and includes status, artifacts, findings, risks,
  and next action.
