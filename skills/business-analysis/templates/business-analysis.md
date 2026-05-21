# Business Analysis
<!-- Analysis of goals, problems, solutions, risks, constraints, and scope. See grace-lifecycle.md Stage 2. -->

> **Methodology Review Status**
> Last comprehensive review: <YYYY-MM-DD> by <reviewer name>
> Covers: traceability + defensibility
<!-- This banner is updated by the analysis-review skill on completion of
     traceability and defensibility checks. Skills that modify this file
     reset the banner to a "Modified since last review" state. See
     docs/03-design/analysis-review-mechanism.md for details. -->

<!--
For an existing product, update this canonical product analysis graph instead
of creating a separate feature-local graph. Preserve existing IDs, reuse or
revise existing elements where appropriate, and add new IDs only when the
current product graph cannot honestly represent the change.
-->

## 1. Goal Hierarchy
<!-- Tree of goals from strategic to operational. Each goal should trace to a parent goal or be a top-level goal. -->

- G-01: (top-level goal)
    - G-02: (sub-goal contributing to G-01)
    - G-03: (sub-goal contributing to G-01)

## 2. Problem Map
<!-- Problems linked to the goals they obstruct. Note conjunctive (AND) or
     disjunctive (OR) causal relationships. The Addressed By column lists the
     solutions addressing this problem; it is the reciprocal of the solution
     row's Addresses column. -->

| ID | Problem | Obstructs | Caused By | Addressed By |
|---|---|---|---|---|
| P-01 | | G-01 | | S-01 |
| P-02 | | G-02 | P-01 | S-03 |

## 3. Solution Inventory
<!-- Each solution must address a problem, advance a goal, or mitigate a risk
     (or any combination). List all upstream IDs the solution targets in the
     Addresses / Mitigates column: P-## or G-## for addresses, R-## for
     mitigates. The reciprocal references must also appear on the
     corresponding Problem, Goal, and Risk rows. Keep solutions substrate-
     agnostic — build-vs-buy classification belongs in release planning,
     not here, so the same solution can carry different posture across
     releases as substrate evolves. -->

| ID | Solution | Addresses / Mitigates |
|---|---|---|
| S-01 | | P-01 |
| S-02 | | G-03 |
| S-03 | | P-02, R-01 |

## 4. Risk Register
<!-- Mitigations are solutions, not separate elements. Record solution IDs in
     the Mitigated By column; the corresponding solution row must list this
     risk in its Addresses / Mitigates column. Disposition is one of:
     Mitigated, Accepted (with rationale), Monitored, Deferred. -->

| ID | Risk | Threatens | Likelihood | Impact | Mitigated By | Disposition |
|---|---|---|---|---|---|---|
| R-01 | | S-01 | | | S-03 | Mitigated |

## 5. Constraint Register
<!-- Constraints are conditions outside the project's sphere of control that
     bound what is reachable. They are not problems themselves but produce
     problems when they collide with goals or solutions. Solutions work
     around constraints; they do not address them. Sphere classification
     distinguishes "influence-but-out-of-scope" (could move to problem
     with scope expansion) from "outside-control-entirely" (truly outside).
     Bounds lists the goal(s) or solution(s) this constraint applies to. -->

| ID | Constraint | Sphere | Bounds | Produces Problems |
|---|---|---|---|---|
| C-01 | | outside-control-entirely | G-01 | P-03 |
| C-02 | | influence-but-out-of-scope | S-02 | P-05 |

## 6. Scope Statement

**In scope:**
-

**Out of scope:**
-

## 7. Traceability Check
<!-- Verify before presenting. Edge-consistency means: every reference in an
     Addresses / Mitigates column has a matching reciprocal reference (e.g.,
     if S-03 lists R-01 in Mitigates, R-01 must list S-03 in Mitigated By). -->

- [ ] Every goal has at least one solution or contributing sub-goal
- [ ] Every problem traces to at least one goal it obstructs
- [ ] Every solution addresses a problem, advances a goal, or mitigates a risk
      (or any combination)
- [ ] Every risk is mitigated by at least one solution, or explicitly accepted
      with rationale
- [ ] Every constraint traces to the goals or solutions it bounds and the
      problems it produces
- [ ] Every solution-to-upstream edge is recorded on both endpoint rows
      (edge consistency)
- [ ] Scope is explicitly defined
- [ ] No orphaned elements
