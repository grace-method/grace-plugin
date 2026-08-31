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

Before writing any test for a criterion, enumerate its enforcement points —
every component that must independently uphold it. More than one enforcement
point -> feature-level criterion -> outer-loop test required before any layer
is implemented. "The user sees X" is always feature-level; a unit test on one
side of the boundary is not sufficient.

When unit tests pass but the product is broken, the gap is usually at the integration layer.
Treat that as a sign that outer-loop tests are missing.

Assert what must be true, not only what must not be. A suite built entirely
from prohibitions passes against an empty system: forbid the wrong inputs and
nothing requires the right ones to be there at all.

A test's name and docstring are claims, and they must not claim more than its
assertions check. A body that asserts less than its name promises is worse
than a missing test: readers and reviewers inventory coverage by those names,
so the overstatement is what convinces everyone the gap is closed. Where a
test is deliberately weaker than its subject for now — awaiting data, or a
component not yet built — say so in the docstring rather than letting the
name carry the claim.

**Write the docstring last, from the assertions.** The overclaim is not
usually a lie; it is a first draft written while the intent was fresh and the
assertions were still imagined. Then the assertions land narrower than the
intent and nobody re-reads the prose. Naming what a test *cannot* catch is
worth as much as naming what it can: an oracle that draws one of its inputs
from the artifact under test is a real contract and a partial one, and only
the docstring can say which half is which. Claiming full independence there
makes the suite look stronger than it is, which is the failure mode the whole
rule exists to prevent.

Two specific overclaims worth checking by habit, because both survive review
easily and both were caught only by an outside reviewer:

- **"Independent" oracles that share an input with their subject.** If a
  derivation is `anchor − activity` and the anchor is transcribed from the
  file being checked, a wrong anchor and a correspondingly wrong result pass
  together.
- **Provenance checks reduced to names.** "From the same shared file" is not
  what `Path(...).name` compares. Two files with one basename satisfy it for
  exactly as long as their contents coincide — which is the drift the check
  was written to prevent.

Fixtures must be traceable to a real example. A fixture hand-written from a
description of a data hazard drifts toward tidiness — the whitespace, ordering,
encoding and absent columns that made the hazard a hazard get smoothed away —
so the test passes while the rule fails on real input. Sample the artifact.
Where a project documents a known data hazard, some test should carry a fixture
that reproduces it.

A release plan's Validation Guide should include a step that demonstrates the
outer-loop test exists and passes, so the human can catch this failure mode
directly rather than relying on the AI's self-report.

Test adequacy is not self-certified. At medium or large change scale, and at
small scale whenever any criterion is feature-level, an independent reviewer
audits the testing before the work is reported complete — use the
`test-adequacy-review` skill. Record the independence actually achieved, and
report a missing reviewer as a blocked gate rather than self-assessing.
