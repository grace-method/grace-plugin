---
name: project-definition
description: >
  Stage 1 product definition for new initiatives. Clarify target users,
  intended value, major task categories, scope intent, and whether the
  initiative should proceed to analysis. Use for project types b through d.
model: opus
context: fork
---

# Product Definition

Use this skill after `project-setup` when a new initiative needs a real Stage 1
product-definition pass before business analysis begins.

Compatibility note: this skill is still named `project-definition` so existing
routes and installed runtimes keep working. The Stage 1 concept is Product
Definition.

## Current Project Context

### Existing Product Definition Summary
!`cat docs/01-project-definition/project-definition-summary.md 2>/dev/null || echo "No existing product definition summary found — starting fresh."`

### Existing Project Thesis And Principles
!`cat docs/01-project-definition/project-thesis-and-principles.md 2>/dev/null || echo "No existing product thesis and principles found — starting fresh."`

## Interaction Pattern

1. Identify the target user or audience.
   - Clarify who the initiative is for and what context they are operating in.
   - Distinguish primary users from secondary stakeholders when that matters.

2. Clarify intended value.
   - State what improvement the initiative is trying to create.
   - Keep the emphasis upstream of requirements and design.

3. Define the major task categories in scope.
   - Describe the main categories of work the system should support first.
   - Do not collapse this into implementation detail or requirement language yet.

4. Clarify initial scope intent for the current phase.
   - Make the current slice explicit.
   - Name meaningful deferred areas so ambition does not silently expand.

5. Survey existing tools or solutions.
   - Identify what already exists.
   - For each meaningful candidate, ask whether it fits as-is, partially fits, or is insufficient.
   - Use the result to frame Adopt / Configure / Extend / Build thinking without forcing premature commitment.

6. Capture thesis and guiding principles.
   - State the core belief behind the initiative.
   - Record product, experience, governance, and evaluation principles that should shape later analysis and design.

7. Decide whether the initiative should proceed to analysis.
   - If the gap is weak or the rationale is not yet credible, say so.
   - If proceeding is justified, make that recommendation explicit.

8. Present the Stage 1 artifacts for approval.
   - Do not treat product definition as settled until a human reviews it.
   - Do not move into Stage 2 analysis until the Stage 1 artifacts are approved enough.

## Outputs

Produce or update these artifacts as needed:

- `docs/01-project-definition/project-definition-summary.md`
- `docs/01-project-definition/project-thesis-and-principles.md`

Use the templates in this skill directory when scaffolding or substantially
reshaping those artifacts.

## Gate

Human approval is required before business analysis begins.
