---
name: testing-strategy
description: >
  Define the project's testing strategy. What test layers exist, what tools
  they use, how acceptance criteria map to tests, and how tests run through
  the delivery workflow. Use during design, alongside or after design-review.
model: sonnet
---

# Testing Strategy

Use this skill during Stage 3 design to make the verification model explicit
before implementation begins.

## Current Project Context

### System Architecture
!`cat docs/03-design/system-architecture.md 2>/dev/null || echo "No system architecture found."`

### Components
!`cat docs/03-design/01-components.md 2>/dev/null || echo "No components artifact found."`

### Interfaces
!`cat docs/03-design/02-interfaces.md 2>/dev/null || echo "No interfaces artifact found."`

### Technology Choices
!`cat docs/03-design/03-technology-choices.md 2>/dev/null || echo "No technology choices artifact found."`

### Existing Testing Strategy
!`cat docs/03-design/05-testing-strategy.md 2>/dev/null || echo "No existing testing strategy found - starting fresh."`

## Interaction Pattern

1. Review the project architecture and its meaningful boundaries.
   - Testing layers should reflect actual risk boundaries, not generic categories pasted in by habit.

2. Define each test layer the project needs.
   - For each layer, state its purpose, tool, when it runs, and what it does not cover.

3. Justify what is included and excluded.
   - Not every project needs every layer.
   - If a layer is omitted, say why that omission is acceptable.

4. Define where acceptance criteria live.
   - Acceptance criteria do not live in the testing strategy.
   - Feature docs own integration criteria.
   - Story docs own component-level criteria.

5. Define the test execution workflow.
   - Describe the inner loop, pre-commit, pre-merge, CI, and any other important gates.

6. Document constraints and tradeoffs.
   - Real DB versus mocks, browser automation choices, cost/speed tradeoffs, and environment needs should be made explicit.

7. Present the testing strategy for approval.
   - Do not treat the verification model as settled until a human reviews it.

## Common Layers Reference

These are guidance, not a mandatory checklist:

- Unit: single function or logic path, no I/O
- Contract: cross-module boundary agreement such as schemas, payloads, or formats
- Integration: real interfaces and infrastructure boundaries
- End-to-end / Smoke: user-facing workflow across the full stack
- Performance: latency, throughput, or resource-constraint verification

## Outputs

Produce or update these artifacts as needed:

- `docs/03-design/05-testing-strategy.md`

Use the template in this skill directory when scaffolding or substantially
reshaping the testing strategy.

## Gate

Human approval is required before treating the testing strategy as settled.
