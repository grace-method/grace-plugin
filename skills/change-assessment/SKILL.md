---
name: change-assessment
description: >
  Assess any code change request before implementation. Classifies scale
  (trivial/small/medium/large), applies a sniff test, and routes to the
  appropriate process depth. Use before accepting any implementation instruction.
model: haiku
---

# Change Assessment

Use this skill before accepting implementation work.

## Interaction Pattern

1. Classify the change scale:
   - **Trivial**: typo, constant, rename -> implement directly
   - **Small**: simple bug fix, field addition, bounded behavior tweak -> apply sniff test, then implement
   - **Medium**: new feature, significant behavior change, new integration -> do lightweight delta business analysis against the existing product graph
   - **Large**: cross-cutting change, new subsystem, architectural shift -> do full business analysis before implementation, updating or re-baselining the product graph

2. For small changes, apply the sniff test:
   - What goal does this serve?
   - Is this treating a symptom or a cause?
   - Will this change make the next change harder or easier?

3. If concerns arise, surface them as an observation, not a hidden veto:
   - "You asked for X. Before I implement it, I notice [concern]. Would you like me to just make the change, or should we look at the broader picture first?"

4. If constraints force a shortcut, apply the Ideal Solution Pattern:
   - name the ideal solution
   - record the expedient choice
   - name the gap as intentional debt

5. Route to the appropriate next step:
   - `trivial` -> implement directly
   - `small` -> implement after sniff test
   - `medium` -> `business-analysis` as a delta against the canonical product graph
   - `large` -> `business-analysis` to update or re-baseline the product graph, then `design-review`

## Output

Produce a brief classification record containing:

- change scale
- rationale
- sniff test answers when applicable
- routing decision
