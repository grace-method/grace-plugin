---
name: implementation
description: >
  Guide TDD implementation. Red-green-refactor cycle, multi-layer testing,
  acceptance criteria at correct levels, Design by Contract, and version
  control discipline. Use when writing code for approved solutions.
model: sonnet
---

# Implementation

Use this skill when approved design work is ready to become working software.

This skill assumes the change has already passed through the earlier lifecycle
gates. It is for disciplined delivery, not for skipping analysis or design.

## Current Project Context

### Approved Business Analysis
!`cat docs/02-analysis/business-analysis.md 2>/dev/null || echo "No business analysis found. Implementation should not proceed for non-trivial work without analysis."`

### Requirements
!`cat docs/02-analysis/requirements/requirements.md 2>/dev/null || echo "No requirements found."`

### Design Review Status
!`cat docs/03-design/design-review-checklist.md 2>/dev/null || echo "No design review checklist found. Confirm design approval before implementation."`

### Design Traceability
!`cat docs/03-design/04-traceability-matrix.md 2>/dev/null || echo "No design traceability matrix found."`

### Testing Strategy
!`cat docs/03-design/05-testing-strategy.md 2>/dev/null || echo "No testing strategy found. Use the project testing approach if defined elsewhere, or note the gap explicitly."`

### Existing Implementation Artifacts
!`find docs/04-implementation/features -name "*.md" | sort 2>/dev/null || echo "No feature or story docs found."`

### Active Technical Debt
!`cat docs/04-implementation/technical-debt.md 2>/dev/null || echo "No technical debt log found."`

## Interaction Pattern

1. Confirm traceability before writing code.
   - Identify which business analysis solution(s), requirement(s), and design element(s) this work addresses.
   - If the work does not trace cleanly, surface that before implementation proceeds.

2. Create or reference the feature doc.
   - Feature docs carry feature-level acceptance criteria for cross-story or integration behaviors.
   - If a feature doc does not exist, create one from the template.

3. Break the feature into stories when needed.
   - Story docs carry single-behavior, single-component acceptance criteria.
   - If the work is already story-sized, reference or create the appropriate story doc directly.

4. Follow TDD for each story.
   - Red: write the minimal failing test from an acceptance criterion. Run it and confirm failure. Do not write implementation yet.
   - Green: write the simplest code that makes the test pass. No more.
   - Refactor: improve structure without changing behavior. All tests must still pass.

5. Apply outer-loop and inner-loop testing where integration boundaries exist.
   - Start from the feature-level acceptance criterion.
   - Write a failing outer-loop test at the appropriate layer.
   - Use inner-loop TDD to drive the implementation.
   - When the inner loop is green, the outer-loop test should pass.
   - If it still fails, the gap is at the integration layer.

6. Record technical debt intentionally.
   - If constraints force an expedient implementation, log the gap between the expedient and the ideal.
   - Tie debt to the relevant feature or story and include a repayment trigger.

7. Present an implementation checkpoint at meaningful milestones.
   - Summarize what was built.
   - Report test results.
   - Confirm traceability.
   - Name debt, blockers, or open issues.
   - Keep this as a structured conversational gate unless a project proves that a persistent artifact is needed.

## Anti-Patterns To Flag

- Writing tests and implementation simultaneously
- Tautological tests
- Skipping the refactor step
- Batching multiple behaviors into one test
- Integration criteria placed at story level
- Component criteria placed at feature level

## Definition Of Done

- All tests pass at every layer defined in the testing strategy
- Code passes automated review
- The feature traces back to business analysis solution(s)
- Documentation is updated when applicable
- Technical debt, if any, is recorded intentionally

## Outputs

Produce or update these artifacts as needed:

- `docs/04-implementation/features/feature-*.md`
- `docs/04-implementation/features/stories/story-*.md`
- `docs/04-implementation/technical-debt.md`

Use the templates in this skill directory when scaffolding or substantially
reshaping those artifacts.

## Gate

Human review is required at meaningful implementation checkpoints before
treating a feature milestone as accepted.
