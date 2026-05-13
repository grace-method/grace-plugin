---
paths:
  - "**/*test*"
  - "**/*spec*"
  - "**/__tests__/**"
  - "**/*.test.*"
  - "**/*.spec.*"
---

<!--
Managed by GRACE — overwritten on every /grace:install run.
This file is owned by the GRACE methodology. Edits will be lost on the next install.
-->

# Test Discipline

- No tautological tests.
- No testing implementation details; test behavior and contracts.
- No batching multiple behaviors into one test.
- Do not skip the refactor step after green.

Acceptance criteria placement:

- Single-component behavior -> story-level criterion -> unit or contract test.
- Cross-component behavior -> feature-level criterion -> smoke or e2e test.
- Wrong: integration criterion at story level.
- Wrong: component criterion at feature level.

When unit tests pass but the product is broken, the gap is usually at the integration layer.
Treat that as a sign that outer-loop tests are missing.
