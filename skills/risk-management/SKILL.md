---
name: risk-management
description: >
  Identify, score, review, and mitigate risks for goals, problems, solutions,
  requirements, designs, releases, or operational decisions. Use from
  business-analysis when creating or reviewing the risk register, and from other
  stages when risk reasoning is needed without rerunning the full Stage 2
  workflow.
model: opus
---

# Risk Management

Use this skill when risk reasoning needs more discipline than a brief mention
inside another workflow. Business analysis remains the owner of the Stage 2 GPSR
workflow; this skill owns the risk technique.

## Current Project Context

### Existing Business Analysis
!`cat docs/02-analysis/business-analysis.md 2>/dev/null || echo "No existing business analysis found."`

### Existing Requirements
!`cat docs/02-analysis/requirements/requirements.md 2>/dev/null || echo "No existing requirements found."`

### Active Release Plans
!`find docs/release-planning -maxdepth 1 -name 'release-*.md' -print -exec sed -n '1,220p' {} \; 2>/dev/null || echo "No release plans found."`

## Core Semantics

- A **risk** is a credible future problem that threatens a goal, solution,
  requirement, design choice, release, operation, or review practice.
- A **mitigation** is a solution targeted at a risk; it reduces the risk's
  likelihood, impact, or both. Mitigations are not a separate node type —
  they are solutions whose purpose includes risk reduction. A solution may
  address problems, advance goals, and mitigate risks in any combination.
- A risk can be caused by a proposed solution. In that case, the risk
  threatens the solution; the solution does not mitigate that risk merely
  because the risk is about the solution.
- A mitigating solution may be a small supporting intervention rather than
  a central product solution. Either way it lives in the Solution Inventory
  with a `mitigates R-##` edge — never as inline prose on a risk row.
- A risk may also be **accepted** with recorded rationale when mitigation is
  judged not worth the cost. Acceptance is a deliberate disposition, not a
  default for unmitigated risks.

## Interaction Pattern

1. Identify candidate risks creatively.
   - Look for risks to goals, solutions, requirements, design choices, release
     scope, operations, and review practices.
   - Include risks introduced by proposed solutions, not only external risks.
   - Ask "what could make this fail, mislead, become burdensome, or create a new
     problem?"

2. Filter negligible risks.
   - Do not force every imaginable risk into the artifact.
   - Preserve risks that are credible enough to affect design, review,
     implementation, validation, or operations.

3. Validate risk relationships.
   - A risk threatens something (goal, solution, requirement, etc.).
   - A mitigation is a solution carrying a `mitigates R-##` edge.
   - If a proposed solution creates a risk, model the risk as threatening
     that solution.
   - Do not record a solution as mitigating a risk unless the solution
     was introduced or selected at least in part to reduce that risk.
     A solution that happens to incidentally reduce a risk is not a
     mitigation.

4. Score meaningful risks.
   - Use `risk score = likelihood x impact` unless the project defines another
     scale.
   - Default to a `1` through `5` scale for likelihood and impact:
     - `1` very low
     - `2` low
     - `3` medium
     - `4` high
     - `5` very high
   - Treat the score as judgment support, not scientific precision.

5. Interpret scores lightly.
   - `1-4`: usually negligible or accepted
   - `5-9`: worth tracking or mitigating cheaply
   - `10-15`: meaningful; mitigation or explicit acceptance expected
   - `16-25`: serious; mitigation strongly expected before proceeding

6. Decide disposition.
   - Mitigate, accept, monitor, defer, or ignore as negligible.
   - Make accepted risks explicit when they are meaningful.

7. Propose proportionate mitigating solutions.
   - A mitigating solution is recorded in the Solution Inventory (a new S-##
     or an existing one) with a `mitigates R-##` edge. Never write mitigation
     prose inline on a risk row.
   - Favor cheap prevention or insurance for medium risks.
   - Avoid heavy mitigation when the risk is credible but not severe.
   - Prefer mitigations that reuse existing solutions, artifacts, review
     practices, source links, checks, or lightweight automation before
     adding new systems.
   - If on review the risk is judged not worth mitigating, propose
     Accepted disposition with a one-line rationale instead of inventing
     a fig-leaf mitigation.

8. Return updates to the owning artifact.
   - Risk entries identify what is threatened and which solutions mitigate
     them (the Mitigated By column).
   - Mitigating solutions list the risk(s) they mitigate in their
     Addresses / Mitigates column.
   - Both directions of each `mitigates` edge must be present for
     bidirectional navigability.
   - Relationship corrections should be explicit when a risk/mitigation
     edge was reversed, missing on one side, or implausible.

9. Reset the review banner on every analysis artifact modified.
   - For each analysis artifact written to (e.g.,
     `docs/02-analysis/business-analysis.md`), replace the current-state
     review banner with a modified-state banner that preserves the
     original review date:
     ```
     > **Methodology Review Status**
     > ⚠ Modified since last review on <prior date>
     > Last comprehensive review by: <prior reviewer>
     > Pending: run /analysis-review to align recent changes
     ```
   - This signals that the artifact contains delta that has not been
     verified through traceability and defensibility checks.
   - The user invokes `analysis-review` afterward to verify the delta and
     return the banner to current state. See
     [analysis-review-mechanism.md](../../../docs/03-design/analysis-review-mechanism.md)
     for design rationale.

## Relationship Checks

- Does each risk have a threatened element?
- Does each risk have at least one mitigating solution OR an explicit
  Accepted disposition with rationale?
- For every `mitigates R-##` edge on a solution, does the risk row list
  that solution in Mitigated By, and vice versa?
- Is any primary solution incorrectly shown as mitigating a risk that it
  actually introduces?
- Are risk likelihood and impact plausible given the project context?
- Is the mitigation effort proportionate to the risk score?
- Are negligible risks being omitted or explicitly accepted rather than
  cluttering the artifact?

## Outputs

Return the smallest useful set of updates:

- risk entries or revised risk entries
- likelihood, impact, score, and disposition for meaningful risks
- relationship corrections, especially `threatens` vs. `mitigates`, and
  edge-consistency between risk rows and the solutions that mitigate them
- mitigating solution candidates (new S-## entries or `mitigates R-##`
  edges added to existing solutions)
- any risks intentionally Accepted (with rationale) or Deferred
