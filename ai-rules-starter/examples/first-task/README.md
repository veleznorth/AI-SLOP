# First Task Example

Fictional request: "Add a missing label to the save button."

Minimal flow:

1. Classifier creates a root packet with the request, constraint, and small diff budget.
2. Reading planner asks to inspect the button component and nearby tests.
3. Reader records where the button is defined and whether a test already exists.
4. Prompt creator writes a final Codex prompt limited to that component.
5. Codex changes only the requested label, runs the named check, and reports the diff.
6. Auditor compares the report with the evidence and ledger entry.

The example is intentionally small: evidence first, then bounded implementation.
