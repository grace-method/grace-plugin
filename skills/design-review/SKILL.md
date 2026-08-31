---
name: design-review
description: >
  Design and architecture review. Walk the operator's procedures, then
  decompose approved business-analysis solutions into components, interfaces,
  ADRs, and technology choices. Use after business analysis is approved and
  before implementation begins.
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

2. Walk the operator's procedures before defining components.
   - For each job in the Product Definition's task inventory, write the steps in the order a human performs them.
   - Check each step against the state the previous step actually leaves behind, not the state the design assumes. Follow the instruction and take the next step — do not reason about the code path instead.
   - Cover the whole calendar, not the representative path: first run, period-end, year-end, recovery, and re-entry after a gap. Take the rarest first; nothing rehearses them.
   - Of a rare step ask: can it be done at all? Of a frequent step ask: what does it cost, and will the friction get worked around?
   - This applies to any human-operated sequence whose steps are separated in time and inherit state from one another. Recurrence is a common cause, not the test.
   - Record the result in `docs/06-operations/user-manual.md`, using the template in this skill directory. The walk is the design activity; the manual is its record.
   - The manual describes what the product does **now**. Behaviour that is designed but has not shipped goes in exactly one block per release, marked `<!-- UNSHIPPED: R-NNN -->` … `<!-- /UNSHIPPED: R-NNN -->`, never inline. Closure removes the block or promotes its text; inline futures become claims the product does not honour, and nothing can find them later.

3. For each solution, or for each coherent cluster of related solutions, define:
   - major components and their responsibilities
   - communication paths between components
   - key boundaries, interfaces, or contracts
   - technology choices with rationale and tradeoffs

4. Apply Design by Contract at important boundaries.
   - identify preconditions
   - identify postconditions
   - identify important invariants where relevant

5. Record significant decisions as ADRs when the choice has meaningful consequences.
   - include context, decision, and consequences
   - An ADR's status is a claim about whether the decision binds. When the design gate accepts one, stamp it `Accepted at the <R-NNN> design gate, <YYYY-MM-DD>` **in the gate's own commit**. Never leave `Proposed` behind after a gate has passed — a reader cannot then tell a settled decision from an open one.

6. Build the design traceability matrix.
   - every component should trace to one or more solutions
   - every mapped component should also trace to requirement(s)

7. Flag gaps.
   - call out solutions that do not yet have an implementing design element
   - call out components or interfaces that do not trace back cleanly

8. Present the design package for approval.
   - do not treat design as settled until a human reviews it
   - do not move into implementation until the design package is approved enough

## Key Questions

- What are the major components and their responsibilities?
- How do they communicate?
- What are the important technical tradeoffs and why?
- What constraints does the chosen technology impose?
- Where are the meaningful boundaries?
- How does a human operate this over time, including the steps that run only once a year?

## Outputs

Produce or update these artifacts as needed:

- `docs/03-design/01-components.md`
- `docs/03-design/02-interfaces.md`
- `docs/03-design/03-technology-choices.md`
- `docs/03-design/04-traceability-matrix.md`
- `docs/03-design/adr/`
- `docs/06-operations/user-manual.md` — created here, maintained for the product's life

Use the templates in this skill directory when scaffolding or substantially reshaping those artifacts.

## Gate

Human approval is required before implementation begins.
