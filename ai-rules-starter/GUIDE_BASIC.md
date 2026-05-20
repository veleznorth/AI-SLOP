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

**Classifier**: Runs once per cohesive human request or feature. It creates the root requirement packet, feature tree, candidate executable leaves, and initial diff estimates without deep codebase reading or implementation.

**Reading Planner**: Chooses concrete reading leaves, may use Pi read-only for bounded exploration, and looks for existing implementation, partial implementation, duplication risk, missing evidence, and required reuse scans.

**Reader**: Executes reading_leaf entries and produces evidence packets with verified, inferred, unknown, contradiction, and risk fields.

**Prompt Creator**: Reads the local GPT-5.5 prompting guide, then builds the final bounded implementer prompt from request, evidence, unknowns, and constraints. Unknowns do not become instructions.

**Implementer**: Codex. Implements only from the final prompt, keeps scope small, and reports proof.

**Auditor**: Reviews the diff, checks, evidence, and remaining risks.

Codex should not skip directly from a human request to implementation when AI Rules is active.
