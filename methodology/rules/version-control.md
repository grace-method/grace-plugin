---
paths:
  - ".git/**"
  - "**/*.patch"
  - ".gitignore"
---

<!--
Managed by GRACE — overwritten on every /grace:install run.
This file is owned by the GRACE methodology. Edits will be lost on the next install.
-->

# Version Control Discipline

- One feature branch per story or feature.
- Commits should be frequent during development.
- Squash commits on merge for clean, comprehensible history.
- Each merge request should be one coherent unit of work: a feature, a story, or an explainable component of a story.
- Never treat "everything I worked on yesterday" as a coherent unit.
- Automated code review is essential. AI-generated code volume makes human-only review unsustainable.
- Human review should focus on design intent, architectural alignment, and domain correctness rather than syntax.
