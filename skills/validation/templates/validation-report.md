# Validation Report

<!-- Confirm that the software achieves the goals defined in the analysis. -->

## 0. Evidence Snapshot

<!-- Summarize the evidence used for this validation. Do not duplicate the full
     evidence log. Link to specific artifacts, commands, or observations where useful. -->

| Evidence area | Summary | Source |
|---|---|---|
| Practice adherence | | |
| Regression protection and test relevance | | |
| Outcome quality | | |
| Orientation and continuity | | |
| Human burden | | |

## 1. Goal Achievement Matrix

| Goal | Status | Evidence |
|---|---|---|
| G-01: (title) | Met / Partially Met / Not Met | |
| G-02: (title) | Met / Partially Met / Not Met | |

## 2. Problem Resolution Status

| Problem | Resolved? | Notes |
|---|---|---|
| P-01: (title) | Yes / Partial / No | |
| P-02: (title) | Yes / Partial / No | |

## 3. Feedback Items
<!-- New problems, goals, or opportunities discovered during validation.
     Each item should feed back to a specific stage: Analysis, Design, or Implementation. -->

| Item | Type | Feed back to | Notes |
|---|---|---|---|
| | New problem / New goal / Bug | Analysis / Design / Implementation | |

## 4. Regression And Test Relevance

<!-- Record whether validation protected existing behavior, not just the new change. -->

- **Latest meaningful test command/result:**
- **Acceptance criteria mapped to tests:**
- **Regression risks assessed:**
- **Regressions caught:**
- **Regressions escaped or not assessed:**

## 5. Human Burden And Orientation

<!-- Record subjective but useful signals about whether GRACE reduced wasted burden
     and helped the user regain context. -->

- **Orientation burden:** Low / Medium / High — rationale:
- **Waste burden:** Low / Medium / High — rationale:
- **Productive judgment preserved:** Yes / Partial / No — rationale:

## 6. Recommendation

<!-- Accept: goals are met, ship it.
     Iterate: specific feedback loop targets identified above.
     Stop: goals cannot be met within current constraints. -->

**Decision:**

**Rationale:**

## 7. Feature Closeout Checklist

<!-- Use this as a lightweight final pass before calling a feature complete.
     It ties together implementation, validation, release communication, and learning. -->

- [ ] The feature doc reflects the final shipped or accepted checkpoint state.
- [ ] Story statuses are updated and incomplete work is either deferred explicitly or moved to backlog.
- [ ] Release notes are understandable to a non-technical stakeholder and updated in the feature doc.
- [ ] A final clean test run was completed and the result was recorded.
- [ ] New defects, incidents, or near misses are linked to backlog or incident artifacts with explicit paths.
- [ ] The feature retrospective exists and captures lessons, tradeoffs, and follow-up actions.
- [ ] Follow-up actions have a destination: backlog, incident, docs update, or code task.
- [ ] The closeout commit name follows the feature convention, typically `Close out <feature-slug> feature`.
