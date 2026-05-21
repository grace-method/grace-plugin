# Causal Defensibility Report
<!-- Apply these four tests to every relationship in the alignment diagram before presenting the business analysis.
     Flag weak links and present them with recommendations. See grace-lifecycle.md Stage 2. -->

> **Methodology Review Status**
> Last comprehensive review: <YYYY-MM-DD> by <reviewer name>
> Covers: traceability + defensibility
<!-- This banner is updated by the analysis-review skill on completion of
     traceability and defensibility checks. Skills that modify this file
     reset the banner to a "Modified since last review" state. See
     docs/03-design/analysis-review-mechanism.md for details. -->

## Execution Tests — Solution → Sub-Goal
<!-- Does executing the solution plausibly and directly cause achievement of the sub-goal? -->

| Solution | Sub-Goal | Finding | Verdict |
|---|---|---|---|
| S-01 | G-02 | | Pass / Weak / Fail |

## Neutralization Tests — Sub-Goal → Problem
<!-- If we achieve the sub-goal, is the problem eliminated or significantly mitigated — or just patched? -->

| Sub-Goal | Problem | Finding | Verdict |
|---|---|---|---|
| G-02 | P-01 | | Pass / Weak / Fail |

## Completeness Tests — Sub-Goals → Goal
<!-- Are the connected sub-goals collectively sufficient to accomplish the parent goal? -->

| Goal | Connected Sub-Goals | Finding | Verdict |
|---|---|---|---|
| G-01 | G-02, G-03 | | Pass / Weak / Fail |

## Strategic Impact Tests — Goal → Higher Goal
<!-- Does achieving this goal directly move the needle on the higher-level goal? -->

| Goal | Higher Goal | Finding | Verdict |
|---|---|---|---|
| G-02 | G-01 | | Pass / Weak / Fail |

## Flagged Weak Links
<!-- Summarize any weak or failing relationships and recommended remediation. -->

| Link | Issue | Recommendation |
|---|---|---|
| | | |
