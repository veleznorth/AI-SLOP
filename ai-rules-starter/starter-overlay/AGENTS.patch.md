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
- If direct numstat misses untracked files, use `git add -N` intent-to-add or an equivalent documented method, and report the method.
- If R1 or R2 is violated, report `R1_EXCEEDED` or `R2_EXCEEDED`.
- The human operator owns session-level context monitoring.
- agents must not invent W/context percentages.
- Reading leaves over 15% W must be routed back to the Reading Planner for split.
- Do not invent architecture, tooling, dependencies, or abstractions outside the prompt.
- Stop if required evidence is missing or constraints conflict.
- Final prompt builder must consult `docs/model-guides/gpt-5.5-prompting-for-ai-rules.md` before producing `final_prompt.md`.
- Pi Agent enforcement is NOT_PROVEN unless a real Pi validation run is recorded.
- Before final reporting, measure the diff with `git diff --numstat`.
- Report status, files changed, main findings, risks/blockers, next action,
  checks executed, and any skipped validation.
<!-- AI_RULES_MANAGED_END -->
