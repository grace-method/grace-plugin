---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
  - "**/*.py"
  - "**/*.go"
  - "**/*.rs"
  - "**/*.java"
  - "**/*.rb"
  - "**/*.swift"
  - "**/*.kt"
---

<!--
Managed by GRACE — overwritten on every /grace:install run.
This file is owned by the GRACE methodology. Edits will be lost on the next install.
-->

# Implementation Discipline

- Never write implementation code without a failing test first (Red-Green-Refactor).
- Each TDD cycle: one behavior per test. Keep cycles small.
- Run the full test suite after each green and refactor step.
- Do not change behavior during refactor; only improve structure.
- If a refactor breaks a test, revert and take a smaller step.
- Strong preconditions at module boundaries. Reject what is outside the module's responsibility.
- Clear postconditions documenting what the module guarantees.
- Fail fast with assertions and guard clauses.
- No dependency without explicit justification.
