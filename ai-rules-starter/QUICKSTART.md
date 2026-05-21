# Quickstart

This guide installs the starter overlay into an existing repo.

## 1. Copy the Overlay

Copy the overlay into the target repo from whichever starting point you are using:

```text
# From inside ai-rules-starter/
starter-overlay/.ai-rules -> .ai-rules

# From the repo root containing ai-rules-starter/
ai-rules-starter/starter-overlay/.ai-rules -> .ai-rules
```

Keep `.ai-rules/` committed so packets, prompts, checklists, and ledger entries are visible to reviewers.

Also copy `docs/model-guides/gpt-5.5-prompting-for-ai-rules.md` into the
target repo at the same path. The final prompt builder must read it before
creating `final_prompt.md`.

Read `docs/operator-context-policy.md` before long tasks. When the operator has
visible context measurements, record them with
`.ai-rules/templates/operator_context_log.yaml`.

## 2. Patch AGENTS.md

Open `starter-overlay/AGENTS.patch.md` from inside `ai-rules-starter/`, or `ai-rules-starter/starter-overlay/AGENTS.patch.md` from the repo root, and insert its managed block into the target repo's `AGENTS.md`.

If the target repo has existing agent rules, append the AI Rules block without deleting local rules.

## 3. Run a First Manual Task

First choose Lite, Standard, or Strict from `docs/workflow-modes.md`. Use the
full operator flow in `docs/manual-operator-runbook.md` when you need a run from
zero.

Use this manual sequence:

1. Write the human request in `.ai-rules/packets/`.
2. Use `classifier.prompt.md` to classify scope.
3. Use `reading-planner.prompt.md` to choose files to inspect.
4. Use `evidence-reader.prompt.md` to collect exactly one evidence packet per
   reading leaf in Standard/Strict.
5. Optional: use Pi read-only with `pi --tools read,grep,find,ls --no-session`
   for planner/reader evidence only. Do not claim Pi enforcement is proven
   until a real Pi validation run is recorded.
6. Use `final-prompt-builder.prompt.md` to produce the implementer prompt.
7. Use a fresh instance before a new phase if the current session is high.
8. Give `final_prompt.md directly` to Codex.
9. Record the result, checks, `numstat_method`, and R2 added + deleted count in
   `.ai-rules/ledger/`.

Do not add automation yet.
