# Fundamentals

## Evidence Before Implementation

Do not implement from guesses. Read the minimum useful context first and record what was observed.

## Separation of Roles

Classification, reading, evidence capture, prompt creation, implementation, and audit are separate responsibilities.

## Small Changes

Prefer small, reviewable diffs. If the required diff grows beyond the current bound, stop and report the risk.

## Claim Status

Use these labels:

- `VERIFICADO`: confirmed by direct evidence.
- `INFERIDO`: reasoned from evidence but not directly observed.
- `DESCONOCIDO`: not known yet.

## Reuse Scan Before New Code

Look for existing patterns, helpers, tests, and docs before adding new code.

## Stopping Is Valid

If evidence is missing, scope is too large, or constraints conflict, stop and report why.
