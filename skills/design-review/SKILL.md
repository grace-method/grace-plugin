---
name: design-review
description: >
  Design and architecture review. Decompose approved business-analysis
  solutions into components, interfaces, ADRs, and technology choices.
  Use after business analysis is approved and before implementation begins.
model: opus
context: fork
---

# Design Review

Use this skill to turn approved Stage 2 analysis into a reviewable Stage 3
design package.

## Approved Business Analysis Solutions
!`cat docs/02-analysis/business-analysis.md 2>/dev/null || echo "WARNING: No business analysis found. Analysis should precede design."`

## Requirements
!`cat docs/02-analysis/requirements/requirements.md 2>/dev/null || echo "No requirements found."`

## Interaction Pattern

1. Review the approved solutions from business analysis.
   - Work from the analysis and requirements rather than inventing a parallel problem framing.

2. For each solution, or for each coherent cluster of related solutions, define:
   - major components and their responsibilities
   - communication paths between components
   - key boundaries, interfaces, or contracts
   - technology choices with rationale and tradeoffs

3. Apply Design by Contract at important boundaries.
   - identify preconditions
   - identify postconditions
   - identify important invariants where relevant

4. Record significant decisions as ADRs when the choice has meaningful consequences.
   - include context, decision, and consequences

5. Build the design traceability matrix.
   - every component should trace to one or more solutions
   - every mapped component should also trace to requirement(s)

6. Flag gaps.
   - call out solutions that do not yet have an implementing design element
   - call out components or interfaces that do not trace back cleanly

7. Present the design package for approval.
   - do not treat design as settled until a human reviews it
   - do not move into implementation until the design package is approved enough

## Key Questions

- What are the major components and their responsibilities?
- How do they communicate?
- What are the important technical tradeoffs and why?
- What constraints does the chosen technology impose?
- Where are the meaningful boundaries?

## Outputs

Produce or update these artifacts as needed:

- `docs/03-design/01-components.md`
- `docs/03-design/02-interfaces.md`
- `docs/03-design/03-technology-choices.md`
- `docs/03-design/04-traceability-matrix.md`
- `docs/03-design/adr/`

Use the templates in this skill directory when scaffolding or substantially reshaping those artifacts.

## Gate

Human approval is required before implementation begins.
