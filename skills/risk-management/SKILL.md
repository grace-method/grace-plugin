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
- A **mitigation** is a solution, control, practice, or design choice introduced
  to reduce a risk's likelihood, impact, or detectability.
- A risk can be caused by a proposed solution. In that case, the risk threatens
  the solution; the solution does not mitigate that risk merely because the risk
  is about the solution.
- A mitigation may be a small supporting solution rather than a central product
  solution.

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
   - A risk threatens something.
   - A mitigation mitigates a risk.
   - If a proposed solution creates a risk, model the risk as threatening that
     solution.
   - Do not model a solution as mitigating a risk unless the solution or control
     was introduced specifically to reduce that risk.

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

7. Propose proportionate mitigations.
   - Favor cheap prevention or insurance for medium risks.
   - Avoid heavy mitigation when the risk is credible but not severe.
   - Prefer mitigations that reuse existing artifacts, review practices,
     source links, checks, or lightweight automation before adding new systems.

8. Return updates to the owning artifact.
   - Risk entries should identify what is threatened.
   - Mitigation entries should identify what risk they mitigate.
   - Relationship corrections should be explicit when a risk/mitigation edge was
     reversed or implausible.

## Relationship Checks

- Does each risk have a threatened element?
- Does each mitigation have a risk it mitigates?
- Is any primary solution incorrectly shown as mitigating a risk that it actually
  introduces?
- Are risk likelihood and impact plausible given the project context?
- Is the mitigation effort proportionate to the risk score?
- Are negligible risks being omitted or explicitly accepted rather than
  cluttering the artifact?

## Outputs

Return the smallest useful set of updates:

- risk entries or revised risk entries
- likelihood, impact, score, and disposition for meaningful risks
- relationship corrections, especially `threatens` vs. `mitigates`
- mitigation solution/control candidates
- any risks intentionally accepted or deferred
