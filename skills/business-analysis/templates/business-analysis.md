# Business Analysis
<!-- Analysis of goals, problems, solutions, risks, and scope. See The_Adaptive_Software_Engineering_Lifecycle.md Stage 2. -->

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
<!-- Problems linked to the goals they obstruct. Note conjunctive (AND) or disjunctive (OR) causal relationships. -->

| ID | Problem | Obstructs | Caused By |
|---|---|---|---|
| P-01 | | G-01 | |
| P-02 | | G-02 | P-01 |

## 3. Solution Inventory
<!-- Each solution must resolve a problem or advance a goal. Classify as Adopt / Configure / Extend / Build. -->

| ID | Solution | Resolves / Advances | Classification |
|---|---|---|---|
| S-01 | | P-01 | Build |
| S-02 | | G-03 | Adopt |

## 4. Risk Register

| ID | Risk | Threatens | Likelihood | Impact | Mitigation |
|---|---|---|---|---|---|
| R-01 | | S-01 | | | |

## 5. Scope Statement

**In scope:**
-

**Out of scope:**
-

## 6. Traceability Check
<!-- Verify before presenting: every goal has a solution or sub-goal; every problem traces to a goal; every solution traces to a problem or goal; every risk has a mitigation; no orphans. -->

- [ ] Every goal has at least one solution or contributing sub-goal
- [ ] Every problem traces to at least one goal it obstructs
- [ ] Every solution traces to a problem it resolves or a goal it advances
- [ ] Every risk has a mitigation plan
- [ ] Scope is explicitly defined
- [ ] No orphaned elements
