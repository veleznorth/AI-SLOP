# AGENTS.md Patch

Insert this managed block into the target repo's `AGENTS.md`.

<!-- AI_RULES_MANAGED_START -->
## AI Rules

- Use `.ai-rules/` for AI Rules artifacts: packets, prompts, checklists, and ledger entries.
- Codex acts as a bounded implementer.
- Implement only from the final prompt produced by the AI Rules flow.
- Do not invent architecture, tooling, dependencies, or abstractions outside the prompt.
- Stop if required evidence is missing or constraints conflict.
- Before final reporting, measure the diff with `git diff --numstat`.
- Report files changed, checks executed, risks, and any skipped validation.
<!-- AI_RULES_MANAGED_END -->
