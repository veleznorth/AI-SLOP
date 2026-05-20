# AGENTS.md Patch

Insert this managed block into the target repo's `AGENTS.md`.

<!-- AI_RULES_MANAGED_START -->
## AI Rules

- Use `.ai-rules/` for AI Rules artifacts: packets, prompts, checklists, and ledger entries.
- Codex acts as a bounded implementer.
- Implement only from the final prompt produced by the AI Rules flow.
- AI Rules v0.4 or any full specification document is reference material only.
- The current phase prompt is the operational authority.
- If reference docs suggest extra work outside allowed files/actions, report `OUT_OF_SCOPE_FOR_THIS_PHASE`.
- R1: New productive files must be <= 500 lines.
- R1: Existing productive files already > 500 lines must not grow without explicit authorization.
- R2: Total executable-leaf diff must be <= 600 lines, calculated as added + deleted from `git diff --numstat`.
- If R1 or R2 is violated, report `R1_EXCEEDED` or `R2_EXCEEDED`.
- Do not invent architecture, tooling, dependencies, or abstractions outside the prompt.
- Stop if required evidence is missing or constraints conflict.
- Final prompt builder must consult `docs/model-guides/gpt-5.5-prompting-for-ai-rules.md` before producing `final_prompt.md`.
- Before final reporting, measure the diff with `git diff --numstat`.
- Report files changed, checks executed, risks, and any skipped validation.
<!-- AI_RULES_MANAGED_END -->
