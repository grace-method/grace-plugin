---
name: business-analysis
description: >
  Run business analysis for a new product or for a medium/large change to an
  existing product. Elicit goals, map problems, propose solutions, identify
  risks, define scope, and update the Stage 2 artifacts. For existing products,
  treat the work as a delta against the canonical product analysis graph unless
  the human explicitly asks for a separate exploratory artifact. Use when
  starting new work or when change-assessment routes here.
model: opus
context: fork
---

# Business Analysis

Use this skill to produce or update the Stage 2 analysis package before design
or non-trivial implementation proceeds.

## Canonical Product Graph Rule

For an existing product, business analysis for an enhancement, feature, defect,
or refactoring is a delta against the product's canonical analysis graph, not a
separate feature-local graph. Preserve existing IDs, reuse or revise existing
goals/problems/solutions/risks/constraints/requirements where appropriate, and
add new elements only when the current product graph cannot honestly represent
the requested change.

Feature, release, or change notes may summarize the slice of the graph in focus,
but they should not become competing sources of truth with their own isolated
numbering scheme.

If a brownfield product has no usable GRACE analysis baseline, create the
thinnest useful product-level baseline first, then analyze the requested change
as a delta against that baseline. Mark incomplete areas explicitly rather than
pretending the whole product has been fully analyzed.

## Current Project Context

### Product Definition Summary
!`cat docs/01-project-definition/project-definition-summary.md 2>/dev/null || echo "No product definition summary found."`

### Project Thesis And Principles
!`cat docs/01-project-definition/project-thesis-and-principles.md 2>/dev/null || echo "No product thesis and principles found."`

### Existing Business Analysis
!`cat docs/02-analysis/business-analysis.md 2>/dev/null || echo "No existing business analysis found — starting fresh."`

### Existing Requirements
!`cat docs/02-analysis/requirements/requirements.md 2>/dev/null || echo "No existing requirements found."`

### Active Technical Debt
!`cat docs/04-implementation/technical-debt.md 2>/dev/null || echo "No debt log found."`

## Interaction Pattern

1. Establish whether this is greenfield analysis or delta analysis.
   - If Stage 2 artifacts already exist, identify the existing goals, problems,
     solutions, risks, constraints, and requirements affected by the requested
     change.
   - Do not start new local numbering for a feature unless no product-level
     analysis baseline exists.

2. Elicit or revise goals hierarchically.
   - Work from strategic outcomes toward operational sub-goals.
   - Keep the goal tree clear enough to support later traceability.
   - For delta analysis, prefer reusing or refining existing goals before adding
     new ones.

3. Identify or revise problems as causal barriers to goals.
   - Distinguish symptoms from root problems when possible.
   - Call out conjunctive or disjunctive causality when it matters.

4. Identify constraints as given conditions outside the project's sphere of control.
   - Distinguish constraints from problems using the why-chain test: trace
     causes backward from a problem; when the next "why" answer points outside
     the sphere of influence, you have crossed into a constraint.
   - For each constraint, record what it bounds (goals or solutions) and the
     problems it produces when colliding with them.
   - Classify sphere as `influence-but-out-of-scope` (could become a problem
     with scope expansion) or `outside-control-entirely` (truly outside).
   - Solutions do not address constraints; they work around them by addressing
     the problems constraints produce.

5. Handle solutions stated upfront as hypotheses.
   - Do not treat an initial solution request as sufficient analysis by itself.
   - Ground it back into goals, problems, and constraints.

6. Propose or revise solutions.
   - Each solution must satisfy at least one of: addresses a problem, advances a goal directly, or mitigates a risk.
   - Run the Solution Alignment Check: no orphaned solution ideas.
   - Keep solutions substrate-agnostic. Build-vs-buy classification is a
     release-time decision and belongs in `release-planning`, not here.

7. Derive or update requirements as the living specification.
   - Functional and non-functional requirements should trace back to solutions.
   - Keep them explicit enough to drive later design and testing.

8. Invoke or follow `risk-management` for risk analysis.
   - Identify credible risks to goals, solutions, requirements, release scope,
     and review practices.
   - Review risk relationships so `threatens` and `mitigates` are not reversed.
   - Score meaningful risks with lightweight likelihood x impact judgment when
     useful.
   - Propose proportionate mitigating solutions, recorded as solutions with
     `mitigates R-##` edges — not as separate elements inline on risk rows.
     Medium risks usually deserve cheap prevention or insurance rather than
     heavy infrastructure. A risk may also be explicitly accepted with
     recorded rationale.

9. Define scope.
   - Record what is in scope for the current phase.
   - Record what is out of scope so valuable but deferred ideas do not drift in silently.

10. Invoke `analysis-review` to run traceability and defensibility verification.
    - For greenfield analysis the entire artifact is the delta; for delta
      analysis the review is scoped to the changes.
    - `analysis-review` performs the traceability check, runs causal
      defensibility tests on the relevant edges, flags weak links, and
      updates the review banner on every modified artifact.
    - This keeps greenfield and delta paths running the same verification
      and avoids duplicating review logic across skills.

11. Present the completed Stage 2 package for approval.
    - Do not treat analysis as settled until a human reviews it.
    - Do not move into design decisions until the analysis is approved.

## Outputs

Produce or update these artifacts as needed:

- `docs/02-analysis/business-analysis.md`
- `docs/02-analysis/alignment-diagram.md`
- `docs/02-analysis/defensibility-report.md`
- `docs/02-analysis/requirements/requirements.md`

Use the templates in this skill directory when scaffolding or substantially reshaping those artifacts.

## Gate

Human approval is required before design begins.
