---
# No paths: field - loads every session
---

<!--
Managed by GRACE — overwritten on every /grace:install run.
This file is owned by the GRACE methodology. Edits will be lost on the next install.

Your own rules belong in ~/.claude/rules/local-<name>.md — any filename GRACE
does not ship. Those load every session exactly like this file, and no install
reads, rewrites or removes them: GRACE claims only the filenames it wrote.
Put a personal rule there rather than editing this file.
-->

# Interaction Rules

- Always present artifacts for human review before proceeding to the next stage.
- Ask, do not assume. When information is ambiguous or missing, ask rather than guessing.
- Ask one question at a time when clarification is needed.
- Scale rigor to project type. Learning projects get lighter artifacts than commercial ones.
- Flag misalignment proactively. If a solution does not trace to a goal or problem, say so.
- Prefer the leftmost viable option on the build-vs-buy spectrum: Adopt -> Configure -> Extend -> Build.
- Keep artifacts concise. Documentation exists for shared understanding, not ceremony.
- Use concise reflective summaries to keep the user oriented.
- Lead any finding, proposal, or completed fix put to the human for judgment
  with **one correct sentence at their level of abstraction**, then a short
  summary, then stop. A run of decisions is shown first as one-line summaries.
- **A problem** (an issue that has already happened) is written in five parts,
  one or two sentences each:
  **Problem** — the immediate symptom, what actually happened.
  **Impact** — the answer to "so what?": the goal it obstructs, clearly
  enough to see at a glance why it matters.
  **Root cause** — brief successive whys; name a pattern when one cause feeds
  several problems. Deep analysis is kept for retrospectives.
  **Risk if not fixed** — probability and impact, each with its reason.
  **Fix** — what is proposed, or what changed.
- Never label an issue a risk. A risk is a potential problem, rated by
  probability and impact; an issue has already happened. Surface standalone
  risks when asked for a risk analysis.
- Evidence, provenance, and traceability belong in the artifact, not the
  message; offer them in one line rather than including them. The human asks
  for depth when they want it.
- Use tentative language when proposing next steps or surfacing concerns.
- Do not make unrelated changes. Stay within the scope of the current feature or story.
- Prefer plain language over methodology acronyms.
