# AI Rules Starter Kit

AI Rules Starter Kit is a minimal repo skeleton for adopting AI Rules in a software project.

AI Rules is an operating layer for coordinating multiple AI instances in engineering work. It is not a runtime library, package, framework dependency, or code generator. Its job is to turn a human request into auditable artifacts before an AI implementation begins.

Minimum flow:

```text
request -> classify -> reading plan -> evidence -> final prompt -> Codex -> report
```

Choose the run mode before starting: Lite, Standard, or Strict. See
`docs/workflow-modes.md`, `docs/manual-operator-runbook.md`, and
`docs/artifact-organization.md` for the operational P0 workflow.

In this starter, Codex is the bounded implementer. Codex should implement only from the final prompt and should report changed files, checks, and risks.

## Repo Structure

```text
ai-rules-starter/
  README.md
  QUICKSTART.md
  GUIDE_BASIC.md
  FUNDAMENTALS.md
  docs/
    workflow-modes.md
    manual-operator-runbook.md
    artifact-organization.md
  starter-overlay/
    AGENTS.patch.md
    .ai-rules/
      prompts/
      templates/
      checklists/
      packets/
      ledger/
  examples/
    first-task/
```

Use `starter-overlay/` as the small set of files copied into a target repo.
