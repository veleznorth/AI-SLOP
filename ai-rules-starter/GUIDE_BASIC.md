# Basic Guide

AI Rules separates thinking roles so implementation starts with evidence.

## Authority Boundaries

A specification source, such as AI Rules v0.4 or another full specification, explains the model and vocabulary.
The phase prompt is the operational authority for the current task: it defines the allowed files, actions, checks, and stop conditions.

Codex must not treat long documents as authorization to expand scope beyond the current phase prompt.

## Size Guardrails

R1 keeps productive files bounded: new productive files stay at 500 lines or less, and existing productive files over 500 lines do not grow without authorization.
R2 keeps each executable leaf reviewable: added + deleted lines from `git diff --numstat` stay at 600 lines or less.

## Roles

**Human**: States the request, constraints, acceptance criteria, and risk tolerance.

**Classifier**: Converts the request into a root packet and decides whether the work should be split.

**Reading Planner**: Chooses what files, docs, or tests must be read before implementation.

**Reader**: Reads only the planned sources and records evidence with claim status.

**Prompt Creator**: Builds the final bounded implementer prompt from request, evidence, and constraints.

**Implementer**: Codex. Implements only from the final prompt, keeps scope small, and reports proof.

**Auditor**: Reviews the diff, checks, evidence, and remaining risks.

Codex should not skip directly from a human request to implementation when AI Rules is active.
