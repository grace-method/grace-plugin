# Alignment Diagram
<!-- Directed graph: goals at top, solutions at bottom, arrows pointing upward showing contribution/resolution.
     Problems sit below the goals they obstruct. Solutions sit below the problems they resolve.
     Risks point at the solutions they threaten. -->

```mermaid
flowchart TD
    G01["G-01: (top-level goal)"]
    G02["G-02: (sub-goal)"]
    G03["G-03: (sub-goal)"]

    P01["P-01: (problem)"]
    P02["P-02: (problem)"]

    S01["S-01: (solution)"]
    S02["S-02: (solution)"]

    R01["⚠ R-01: (risk)"]

    S01 --> P01
    S02 --> G03
    P01 --> G01
    P02 --> G02
    G02 --> G01
    G03 --> G01
    R01 -.-> S01
```
