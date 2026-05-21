# Reading Planner Checklist

Before using Pi:

- Confirm Pi is acting only as reader/planner.
- Confirm `allowed_scope`.
- Confirm `disallowed_scope`.
- Confirm the read-only Pi command.
- Confirm Pi exploration is read-only and bounded.
- Confirm Pi read-only.
- Confirm bash, write, and edit will not be used.
- Confirm the expected output format.
- Confirm the operator can measure visible W in the harness.
- Confirm Pi enforcement is not claimed proven without a real validation run.

- Relevant repo instructions included.
- Existing files searched before new code.
- candidate existing implementation recorded or ruled out.
- candidate partial implementation recorded or ruled out.
- duplication risk recorded or ruled out.
- `reuse_scan_required` marked for behavior changes.
- Tests or docs identified when available.
- Each reading leaf has a reason.
- Each reading leaf includes `expected_output.artifact_type: evidence_packet`.
- Each reading leaf includes `expected_output.count: 1`.
- `disallowed_scope` does not block the Reader from creating the expected
  evidence packet.
- `allowed_scope` has <= 3-5 relevant files, or the leaf is split.
- Each `reading_leaf` has one primary question.
- Leaves are split by decision/evidence, not by a fixed universal partition.
- UI/backend/tests are not mixed in one leaf when decisions are independent.
- Separate `reuse_scan` leaf created when duplication risk applies.
- `context_budget.planner_estimate` is low, medium, or high.
- Evidence gaps are named.
- No implementation steps added.
- Risky external tools are flagged.
- Plan is small enough to execute.
- Operator response is short and includes status, artifacts, findings, risks,
  and next action.
