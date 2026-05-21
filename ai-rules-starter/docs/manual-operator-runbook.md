# Manual Operator Runbook

This runbook lets a human operate AI Rules without a separate assistant.

## Start

1. Choose `Lite`, `Standard`, or `Strict` from `docs/workflow-modes.md`.
2. Open a fresh Classifier instance.
3. Use `.ai-rules/prompts/classifier.prompt.md`.
4. Give it the human request, allowed/prohibited files, acceptance criteria,
   diff limit, and any repo instructions.
5. Expect requirement, feature/leaf, initial diff estimate, and proceed/STOP.

Every instance must answer the operator with: `status`, files created or
modified, main findings, risks/blockers, and recommended next action.

For any role, `PASS` means advance to the next role named by the runbook.
`STOP` means do not advance; return to the role named by `stop_label`, or to
the human when the stop requires a decision.

## Reading Planner

Open a Reading Planner instance when the mode or task needs evidence before
implementation.

- Prompt: `.ai-rules/prompts/reading-planner.prompt.md`.
- Input: classifier artifacts, repo map or file list, mode, and constraints.
- Output: one or more `reading_leaf` artifacts.
- Each leaf must include `expected_output.artifact_type: evidence_packet` and
  `expected_output.count: 1`.

The planner splits by decision/evidence when a leaf may be oversized. It does
not create evidence packets.

## Reader

Open one Reader instance per `reading_leaf` in Standard and Strict.

- Prompt: `.ai-rules/prompts/evidence-reader.prompt.md`.
- Input: exactly one `reading_leaf` plus the allowed sources.
- Output: exactly one `evidence_packet`.
- Reader `PASS` -> send the next `reading_leaf`, or continue to Final Prompt
  Builder when all leaves have clean packets.
- Reader `READ_LEAF_OVERSIZE` -> return to the Reading Planner. The Reader does
  not split the leaf.

## Final Prompt Builder

Open a fresh Final Prompt Builder instance when evidence is complete or context
is high.

- Prompt: `.ai-rules/prompts/final-prompt-builder.prompt.md`.
- Input: human request, classifier artifacts, clean evidence packets, unknowns,
  mode, and diff budget.
- Output: `final_prompt.md`.
- Final Prompt Builder `STOP` -> return to the role indicated by `stop_label`.
- Oversized packets are replan signals, not clean authority.

Give `final_prompt.md directly` to the Implementer. Do not wrap it with new
scope, new evidence, or extra files; only add the minimum role instruction if
the harness requires one.

## Implementer

Open the Implementer with only the final prompt and required repo context.

- Prompt/input: `final_prompt.md directly`.
- Expected output: code/doc changes, checks, changed files, risks, and
  `diff_result`.
- If `R2_EXCEEDED`, stop. Do not start another task or keep implementing.

For untracked files, measure diff with direct `git diff --numstat` only when it
sees the files. Otherwise use `git add -N` intent-to-add or an equivalent
documented method. R2 is `added + deleted`.

## Auditor

Open Auditor after implementation.

- Input: final prompt, diff, checks, `diff_result`, and relevant artifacts.
- Output: PASS, PASS_WITH_WARNINGS, or FAIL with evidence.
- Register `diff_result` and a ledger entry when the run requires calibration.

## Opening New Instances

Open a new instance when changing role, when visible context is high, after an
oversized leaf, before implementation in Standard/Strict, or before audit when
the implementation context is noisy.
