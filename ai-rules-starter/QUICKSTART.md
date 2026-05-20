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

## 2. Patch AGENTS.md

Open `starter-overlay/AGENTS.patch.md` from inside `ai-rules-starter/`, or `ai-rules-starter/starter-overlay/AGENTS.patch.md` from the repo root, and insert its managed block into the target repo's `AGENTS.md`.

If the target repo has existing agent rules, append the AI Rules block without deleting local rules.

## 3. Run a First Manual Task

Use this manual sequence:

1. Write the human request in `.ai-rules/packets/`.
2. Use `classifier.prompt.md` to classify scope.
3. Use `reading-planner.prompt.md` to choose files to inspect.
4. Use `evidence-reader.prompt.md` to collect evidence.
5. Optional: use Pi read-only with `pi --tools read,grep,find,ls --no-session`
   for planner/reader evidence only.
6. Use `final-prompt-builder.prompt.md` to produce the implementer prompt.
7. Give only that final prompt to Codex.
8. Record the result and checks in `.ai-rules/ledger/`.

Do not add automation yet.
