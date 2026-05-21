# Spanish Copy Smoke Test Artifact Index

run_id: spanish-copy-smoke-test
mode: Standard-like smoke test

This index documents the existing flat artifact layout. Do not move artifacts
01-30; future runs should use role/fase folders.

## Classifier

- `01-root_requirement_packet.yaml`: root request packet, clean context.
- `02-feature_node.yaml`: feature breakdown, clean context.
- `03-executable_leaf.yaml`: executable leaf, clean context.
- `04-diff_estimate.initial.yaml`: initial diff prediction, context.

## Reading Planner, Round 1

- `05-reading_leaf-source-inventory.yaml`: source inventory leaf, superseded by
  replan.
- `06-reading_leaf-target-state.yaml`: target state leaf, context.
- `07-reading_leaf-translation-constraints.yaml`: translation constraints leaf,
  superseded by replan.

## Reader, Round 1

- `08-evidence_packet-source-inventory.yaml`: READ_LEAF_OVERSIZE; replan
  signal, not clean authority.
- `09-evidence_packet-target-state.yaml`: limited clean evidence.
- `10-evidence_packet-translation-constraints.yaml`: READ_LEAF_OVERSIZE;
  replan signal, not clean authority.

## Final Prompt Builder Stop

- `11-final_prompt_builder_stop_report.md`: STOP report that correctly blocks
  oversized evidence as clean authority.

## Reading Planner, Replan

- `12-reading_leaf-root-docs-inventory.yaml`
- `13-reading_leaf-docs-folder-inventory.yaml`
- `14-reading_leaf-model-guides-inventory.yaml`
- `15-reading_leaf-overlay-inventory.yaml`
- `16-reading_leaf-commands-paths-constraints.yaml`
- `17-reading_leaf-stop-labels-markers-constraints.yaml`
- `18-reading_leaf-numeric-constants-constraints.yaml`
- `19-reading_leaf-spanish-copy-notice-constraints.yaml`

Artifacts 12-19 are the smaller replan leaves.

## Reader, Clean Evidence Set

- `20-evidence_packet-root-docs-inventory.yaml`
- `21-evidence_packet-docs-folder-inventory.yaml`
- `22-evidence_packet-model-guides-inventory.yaml`
- `23-evidence_packet-overlay-inventory.yaml`
- `24-evidence_packet-commands-paths-constraints.yaml`
- `25-evidence_packet-stop-labels-markers-constraints.yaml`
- `26-evidence_packet-numeric-constants-constraints.yaml`
- `27-evidence_packet-spanish-copy-notice-constraints.yaml`

Packets 20-27 are the clean evidence set for the implementable prompt.

## Final Prompt Builder

- `28-diff_estimate.pre_prompt.yaml`: pre-implementation diff estimate.
- `29-final_prompt.md`: final prompt implementable by Codex.

## Auditor

- `30-viability_audit_report.md`: viability audit report.

## Implementer Output

- `ai-rules-starter-es/`: output produced by the implementer. It is outside
  this starter path and is not moved by this index.

## NOT_PROVEN

- Pi Agent real execution: NOT_PROVEN in this smoke test.
- Pi enforcement must not be declared proven until a real Pi validation run is
  executed and recorded.
