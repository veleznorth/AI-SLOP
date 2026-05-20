# Operator Context Policy

status: empirical provisional policy

## W Definition

`W` is the effective visible/usable context window of the current model plus
harness. It is not only the maximum context window advertised for the model.

`W` includes model + harness + tools + loaded context + outputs + history.

Agents do not self-measure `W` unless the harness exposes it explicitly.
agents must not invent W/context percentages. The human operator owns
session-level context control through the harness UI or explicit operator
reports.

## Formal Reading Leaf Budget

These limits apply formally to `reading_leaf` execution:

- 8% of W = proxy planning limit.
- 10% of W = exact-count target.
- 15% of W = hard cap.

Example with `W = 258,400`:

- 8% = 20,672.
- 10% = 25,840.
- 15% = 38,760.

## Provisional Phase Policy

- Classifier: one cohesive request/feature per instance; no deep codebase read.
- Reading Planner: Pi read-only; broad but shallow exploration.
- Reader: one `reading_leaf` per run when possible.
- Final Prompt Builder: use a fresh instance if evidence packets are large.
- Implementer/Codex: do not start implementation if the operator sees high
  context.
- Auditor: may run in a fresh instance with minimal reports/diffs.

This session-level policy is empirical, provisional, and calibrable by role and
harness.

Codex has high overhead. Prefer Pi Agent for read-only exploration and reading.

## Oversize Rule

If the operator marks a `reading_leaf` over 15% W, evidence is not clean
authority.

The reader reports `READ_LEAF_OVERSIZE` or `CONTEXT_LIMIT_EXCEEDED`, records
what was verified, and does not split the leaf.

The operator routes the case back to the Reading Planner. The Reading Planner
splits the `reading_leaf` and emits smaller leaves.

## Planner Prevention

The planner explores to ask better questions; the reader reads to answer
questions.

The planner should prevent oversize with shallow Pi exploration:

- Split if `allowed_scope` has more than 3-5 relevant files.
- Split if one question contains multiple independent decisions.
- Split if UI/backend/tests are mixed and represent independent decisions.
- Create a separate `reuse_scan` when duplication risk exists.
- Prefer `grep`/`find` over deep read during planning.
