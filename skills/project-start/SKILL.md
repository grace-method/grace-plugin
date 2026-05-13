---
name: project-start
description: >
  Start a new GRACE-governed initiative without requiring the human to remember
  the opening skill sequence. Use this at the beginning of a new project or
  when an existing project has unclear methodology setup.
model: opus
---

# Project Start

Use this skill as the friendly front door for new GRACE work.

It does not replace `project-setup` or `project-definition`. It routes the
session through them in the expected order so the human does not need to
remember the startup choreography.

## Interaction Pattern

1. Run `project-setup`.
   - Classify project type.
   - Set the predictability dial.
   - Choose standard structure or custom artifact mapping.
   - Scaffold starter artifacts when appropriate.
   - Get human approval for the artifact strategy.

2. Run `project-definition`.
   - Identify target users and stakeholders.
   - Clarify intended value.
   - Define major task categories and initial scope intent.
   - Survey existing tools or solutions.
   - Capture thesis and guiding principles.
   - Decide whether to proceed to analysis.

3. Stop at the Stage 1 gate.
   - Present the product-definition artifacts for human review.
   - Do not move into business analysis until Stage 1 is approved enough.

## Output

Produce the same artifacts owned by the underlying skills:

- project-setup record or artifact strategy statement
- `docs/01-project-definition/project-definition-summary.md`
- `docs/01-project-definition/project-thesis-and-principles.md`

## Gate

Human approval is required before treating Stage 1 as complete or moving into
business analysis.
