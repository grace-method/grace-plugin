---
name: release-planning
description: >
  Define or revise a release, phase, or delivery slice. Selects a coherent
  product-graph focus, often from solutions but also from requirements, risks,
  defects, validation findings, or design options, then scopes the stage work
  needed to deliver it.
model: opus
---

# Release Planning

Use this skill when the user wants to define the next release, choose what to
build next, implement one or more approved solutions, revise release scope, or
turn a roadmap idea into a scoped delivery plan.

## Purpose

Release planning is a cross-stage discipline. It selects a coherent slice of
the approved product graph for near-term delivery. The lifecycle stages still
govern the quality of work; the release plan governs which work is in focus now.

## Inputs

Prefer current project artifacts over conversation memory:

- `docs/01-project-definition/`
- `docs/02-analysis/business-analysis.md`
- `docs/02-analysis/requirements/requirements.md`
- `docs/02-analysis/defensibility-report.md`
- `docs/03-design/`
- `docs/04-implementation/features/`
- `docs/05-validation/`
- `docs/06-operations/`
- `docs/BACKLOG.md`

## Interaction Pattern

1. Identify the release trigger.
   - solution or solution set
   - requirement subset
   - risk or mitigation
   - defect, validation finding, or incident
   - design option or prototype
   - external milestone or operational need

2. Select the focus nodes.
   - Name the primary graph nodes in scope.
   - Trace each to relevant goals, problems, requirements, risks, and existing design or implementation artifacts.
   - Flag orphaned or weakly traced scope before accepting it into the release.

3. Define release intent and boundaries.
   - State the release objective in outcome terms.
   - Record why this release matters now.
   - Separate in-scope, out-of-scope, deferred, and explicitly rejected work.

4. Classify in-scope solutions on the build-vs-buy spectrum.
   - For each in-scope solution, choose Adopt / Configure / Extend / Build
     per the Solution Types taxonomy in `grace-lifecycle.md`.
   - Prefer the leftmost viable option. Explain why a more rightward option
     is justified when used.
   - Classification is a release-time decision, not a property of the
     solution definition. The same solution may take different posture in
     different releases as substrate (vendors, platforms, tools) evolves.
   - The chosen posture determines the downstream activity profile (vendor
     evaluation, platform configuration, custom development) reflected in
     the work breakdown below.

5. Create the release-to-stage work breakdown.
   - Analysis: what must be confirmed, refined, or added?
   - Design: what design decisions, prototypes, or reviews are needed?
   - Implementation: what features, stories, or code changes are expected?
   - Validation: what evidence will show the release worked?
   - Operations: what readiness, runbook, deployment, or support work is needed?
   - Retirement: what old capability, artifact, or process must be closed, if any?

6. Identify risks, dependencies, and assumptions.
   - Include scope risk, sequencing risk, technical risk, artifact drift risk, and human-review risk where relevant.
   - Prefer mitigation plans over vague risk labels.

7. Write or update the release plan.
   - Use `docs/release-planning/`.
   - Use `templates/release-plan.md` as the starting shape when creating a new release plan.
   - Keep the plan concise enough to guide work without becoming a second copy of every stage artifact.

8. Present for human review.
   - Do not treat the release scope as approved until the human accepts it.
   - After approval, route to the needed stage skills.

## Output

Produce or update a release plan containing:

- release name and status
- release objective
- selected focus nodes
- rationale
- scope boundaries
- release-to-stage work breakdown
- risks, dependencies, and assumptions
- success criteria
- next routing decision

## Gate

Human approval is required before treating the release plan as the active scope
for downstream design, implementation, validation, or operations work.
