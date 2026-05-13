---
name: validation
description: >
  Validate that implemented software achieves analysis goals. Assess goal
  achievement, problem resolution, and feedback loops after implementation
  milestones or before release.
model: opus
context: fork
---

# Validation

Use this skill after meaningful implementation milestones or before release to
check whether the implemented work actually achieved the goals defined earlier
in the lifecycle.

## Current Project Context

### Analysis Goals And Problems
!`cat docs/02-analysis/business-analysis.md 2>/dev/null || echo "No business analysis found."`

### Requirements
!`cat docs/02-analysis/requirements/requirements.md 2>/dev/null || echo "No requirements found."`

### Implementation Status
!`find docs/04-implementation/features -name "*.md" -exec head -5 {} + 2>/dev/null || echo "No features found."`

### Evidence Log
!`cat docs/05-validation/evidence-log.md 2>/dev/null || echo "No evidence log found."`

## Interaction Pattern

1. Review available evidence.
   - Use the evidence log, feature/story artifacts, test output, handoff notes, and implementation records.
   - Separate direct evidence from subjective judgment.
   - If evidence is missing, say so rather than inferring success from artifact presence.

2. For each meaningful goal in the business analysis, assess status.
   - Mark it as met, partially met, or not met.
   - Tie each judgment to concrete evidence.

3. For each meaningful problem, assess whether the implemented solutions actually resolve it.
   - Separate implementation completion from problem resolution.
   - If the software shipped but the problem remains, say so explicitly.

4. Assess regression protection and test relevance.
   - Record the latest meaningful test command and result when available.
   - Note whether tests map to important criteria or only prove superficial behavior.
   - Identify regressions caught, regressions escaped, or regression risk not assessed.

5. Assess human burden and orientation.
   - Record whether artifacts made resumption easier or harder.
   - Distinguish useful human judgment from avoidable process friction.

6. Identify feedback items.
   - Capture new problems, new goals, missed assumptions, or opportunities discovered during validation.

7. Route feedback items to the right stage.
   - Analysis if the problem framing or goals need revision.
   - Design if the architecture or decomposition is insufficient.
   - Implementation if the fix is mostly executional.

8. Apply feature closeout thinking.
   - Confirm feature and story status, release notes, final test state, and retrospective readiness.

9. Recommend the next decision.
   - Accept when goals are met well enough.
   - Iterate when specific follow-up targets are clear.
   - Stop when the current path is not justified under present constraints.

10. Present the validation report for approval.
   - Human review decides whether to accept, iterate, or stop.

## Outputs

Produce or update these artifacts as needed:

- `docs/05-validation/evidence-log.md`
- `docs/05-validation/validation-report.md`

Use the templates in this skill directory when scaffolding or substantially
reshaping validation artifacts.

## Gate

Human review is required before treating the validation outcome as settled.
