# Alignment Diagram

> **Methodology Review Status**
> Last comprehensive review: <YYYY-MM-DD> by <reviewer name>
> Covers: traceability + defensibility
<!-- This banner is updated by the analysis-review skill on completion of
     traceability and defensibility checks. Skills that modify this file
     reset the banner to a "Modified since last review" state. See
     docs/03-design/analysis-review-mechanism.md for details. -->

<!-- Directed graph: goals at top, solutions at bottom, arrows pointing
     upward showing how solutions address problems and advance goals.
     Problems sit below the goals they obstruct.
     Risks are connected to the solutions that mitigate them via a
     `mitigates` edge from the solution to the risk.
     Mitigations are not separate nodes — a solution that mitigates a risk
     simply carries a mitigates edge. -->

```mermaid
flowchart TD
    G01["G-01: (top-level goal)"]
    G02["G-02: (sub-goal)"]
    G03["G-03: (sub-goal)"]

    P01["P-01: (problem)"]
    P02["P-02: (problem)"]

    S01["S-01: (solution)"]
    S02["S-02: (solution)"]
    S03["S-03: (solution addressing P-02 and mitigating R-01)"]

    R01["⚠ R-01: (risk)"]

    S01 --> P01
    S02 --> G03
    S03 --> P02
    S03 -. "mitigates" .-> R01
    P01 --> G01
    P02 --> G02
    G02 --> G01
    G03 --> G01
```
