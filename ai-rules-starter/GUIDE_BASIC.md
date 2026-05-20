# Basic Guide

AI Rules separates thinking roles so implementation starts with evidence.

## Roles

**Human**: States the request, constraints, acceptance criteria, and risk tolerance.

**Classifier**: Converts the request into a root packet and decides whether the work should be split.

**Reading Planner**: Chooses what files, docs, or tests must be read before implementation.

**Reader**: Reads only the planned sources and records evidence with claim status.

**Prompt Creator**: Builds the final bounded implementer prompt from request, evidence, and constraints.

**Implementer**: Codex. Implements only from the final prompt, keeps scope small, and reports proof.

**Auditor**: Reviews the diff, checks, evidence, and remaining risks.

Codex should not skip directly from a human request to implementation when AI Rules is active.
