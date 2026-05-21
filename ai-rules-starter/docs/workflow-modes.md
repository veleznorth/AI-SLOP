# Workflow Modes

Choose the mode before starting a run. The mode sets how much ceremony is
useful; it does not authorize scope beyond the current human request.

| Mode | Use when | Required roles | Minimum artifacts | Typical stop conditions |
| --- | --- | --- | --- | --- |
| Lite | The task is small, low-risk, localized, and mostly documentary or mechanical. | Human, Classifier, Implementer; Reader/Prompt Builder may be batched only if declared. | request note, brief scope/diff estimate, final response; optional `final_prompt.md` when handoff is needed. | scope unclear, R2 likely exceeded, missing required file. |
| Standard | The task needs repo evidence, reusable patterns, or multiple files, but has normal risk. | Human, Classifier, Reading Planner, one Reader per `reading_leaf`, Final Prompt Builder, Implementer, Auditor. | requirement packet, reading leaves, evidence packets, `final_prompt.md`, `diff_result`. | `READ_LEAF_OVERSIZE`, insufficient evidence, final prompt builder STOP, R1/R2 risk. |
| Strict | The task touches contracts, auth, data integrity, migrations, generated APIs, security, or high-blast-radius behavior. | Human, Classifier, Reading Planner, separate Reader instances, Final Prompt Builder, Implementer, Auditor; extra specialist review if named by the prompt. | all Standard artifacts plus artifact index, explicit stop ledger/diff ledger, named unresolved risks. | unknown contract, missing test/runtime proof, oversized evidence, R1/R2 exceeded, human decision required. |

## What Can Be Skipped

- Lite may skip separate reader instances when the task is simple and the
  operator declares the evidence batch in the final response.
- Lite may skip `reading_leaf` files when the needed evidence is a small,
  obvious file set and the implementer can cite what was checked.
- Standard and Strict must not skip evidence before implementation.
- Strict must not batch independent reading leaves into one Reader instance.

## When Strict Is Mandatory

Use Strict when the task can break production contracts, authorization,
ownership, persistence, migrations, external integrations, generated code,
shared schemas, or user data. Use Strict when a human explicitly asks for
auditable proof, real runtime validation, or enforcement validation.
