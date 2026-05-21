# Artifact Organization

New runs should store artifacts by role and phase:

```text
artifacts/
  classifier/
  reading-planner/
  reader/
  final-prompt-builder/
  implementer/
  auditor/
```

Do not mix new run artifacts as one flat sequence unless the operator explicitly
chooses a Lite run and records that choice.

Each artifact should declare:

- `artifact_id`
- `producer_role`
- `phase`
- `status`
- authority level: clean, oversized, superseded, context-only, final, or
  NOT_PROVEN

Use `.ai-rules/templates/artifact_index.yaml` to keep a human-readable trail
across role folders.

The current Spanish copy smoke test remains physically flat for auditability.
Do not move artifacts 01-30. Its flat layout is documented by
`examples/spanish-copy-smoke-test/artifacts/00-artifact_index.md`.
