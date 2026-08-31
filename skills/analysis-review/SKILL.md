---
name: analysis-review
description: >
  Delta-focused review of an existing business analysis. Use when the user
  wants to add new analysis elements (problem, goal, solution, risk,
  constraint, requirement) against an already-approved analysis, when an
  analysis artifact's review banner is in the modified state, or when
  change-assessment routes a medium-scale change that touches analysis.
  Walks the user through capture and alignment in a single flow, runs
  traceability and defensibility checks scoped to the delta, and updates
  the review banner on completion.
model: opus
context: fork
---

# Analysis Review

Use this pot to verify alignment of a delta against an existing analysis.
It performs the same traceability and defensibility verification that
`business-analysis` does at greenfield approval — scoped to what is new or
changed, not the entire graph.

## Canonical Product Graph Rule

For an existing product, additions are deltas against the canonical analysis
graph, not a separate feature-local graph. Preserve existing IDs, reuse or
revise existing goals/problems/solutions/risks/constraints/requirements
where appropriate, and add new elements only when the current graph cannot
honestly represent the change.

## Current Project Context

### Business Analysis
!`cat docs/02-analysis/business-analysis.md 2>/dev/null || echo "No business analysis found — analysis-review requires existing analysis."`

### Requirements
!`cat docs/02-analysis/requirements/requirements.md 2>/dev/null || echo "No requirements found."`

### Causal Defensibility Report
!`cat docs/02-analysis/defensibility-report.md 2>/dev/null || echo "No defensibility report found."`

### Alignment Diagram
!`cat docs/02-analysis/alignment-diagram.md 2>/dev/null || echo "No alignment diagram found."`

## Interaction Pattern

1. Identify the review mode.
   - **Capture mode:** the user has named an element they want to add or
     change ("I want to add a problem about X"). Skill walks through
     capture with alignment in the same flow.
   - **Recovery mode:** an artifact's banner is in the modified state, or
     the banner date is older than the artifact's git modification history.
     Skill detects the delta and presents it for confirmation.

2. For recovery mode, detect the delta from git.
   - For each analysis artifact in scope, locate the post-review anchor
     commit with `git blame -L "Last comprehensive review" -- <artifact>`.
   - Compute `git diff <anchor-commit> -- <artifact>` to get the textual
     delta (covers both committed and uncommitted changes).
   - Parse the diff for added, removed, or changed table rows keyed by ID
     (P-##, S-##, R-##, C-##, G-##, requirement IDs). Note non-tabular
     changes separately.
   - Present the detected delta to the user for confirmation:
     "Since YYYY-MM-DD, I detected: <list>. Confirm or correct."
   - User confirms or amends. Detection covers common cases; amendment
     exists for semantic shifts in unchanged prose.

3. For capture mode, walk the user through alignment one link at a time.
   - For a new problem: which goal does it obstruct? what solutions
     address it? what risks accompany those solutions? what mitigates the
     risks?
   - For a new solution: which problem, goal, or risk does it address or
     mitigate? has the Solution Alignment Check been satisfied?
   - For a new risk: what does it threaten? what mitigates it (or is it
     explicitly accepted with rationale)?
   - For a new constraint: what does it bound? what problems does it
     produce? what is its sphere classification?
   - For a new requirement: what upstream goals/problems/risks/scope
     boundaries/constraints justify it? what solutions realize it?
   - Write each new element into the appropriate artifact with reciprocal
     references on both endpoint rows.

4. Run the traceability check on the confirmed delta.
   - Every new goal has a solution or contributing sub-goal.
   - Every new problem traces to at least one goal it obstructs.
   - Every new solution traces to at least one of: a problem it addresses,
     a goal it advances, or a risk it mitigates.
   - Every new risk is mitigated by at least one solution, or explicitly
     accepted with rationale.
   - Every new constraint traces to the goals or solutions it bounds and
     the problems it produces.
   - Every new requirement has upstream justification and downstream
     realization.
   - Every new edge is recorded on both endpoint rows (edge consistency).
   - No new orphans introduced.

5. Run defensibility tests on the delta's new edges.
   - Execution Test: Solution -> Sub-Goal?
   - Neutralization Test: Sub-Goal -> Problem eliminated or significantly
     mitigated?
   - Mitigation Test: Solution -> Risk likelihood or impact credibly
     reduced?
   - Constraint Boundary Test: Constraint actually outside the sphere of
     influence at current scope, and produces (not merely accompanies) the
     problem?
   - Requirement Derivation Test: Requirement justified by upstream
     elements?
   - Requirement Realization Test: Requirement plausibly realized by
     downstream solutions?
   - Completeness Test: Connected sub-goals sufficient for parent goal?
     (Run if the delta involves new sub-goals.)
   - Strategic Impact Test: Goal moves the needle on the higher goal?
     (Run if the delta involves new top-level goals.)

6. Flag weak links with recommendations.
   - Preserve weak links if they are still directionally useful, but say
     why they are weak and recommend what would make them stronger.

7. Update the review banner on every modified artifact.
   - Replace the modified-state header (or any prior current-state line)
     with a current-state banner:
     ```
     > **Methodology Review Status**
     > Last comprehensive review: <today> by <git config user.name>
     > Covers: traceability + defensibility
     > Recent delta: <list of added/changed IDs>
     ```
   - The "Recent delta" line records the IDs reviewed in this pass.

8. Present the completed review for human approval.
   - Do not treat the delta as approved until the human accepts it.
   - If the delta represents a non-trivial change in scope (new top-level
     goal, new major problem cluster, structural revision), advise the
     human to consider running full `business-analysis` instead.

## Outputs

Updated analysis artifacts:

- `docs/02-analysis/business-analysis.md`
- `docs/02-analysis/requirements/requirements.md`
- `docs/02-analysis/defensibility-report.md`
- `docs/02-analysis/alignment-diagram.md`

Each modified artifact carries an updated review banner.

## Gate

Human approval is required before treating the reviewed delta as part of
the approved analysis baseline. After approval, downstream design,
implementation, or release-planning work may proceed against the updated
graph.

## Related

- See [analysis-review-mechanism.md](../../../docs/03-design/analysis-review-mechanism.md) for the design rationale, banner format reference, and trade-offs considered.
