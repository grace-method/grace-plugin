---
name: retirement
description: >
  Evaluate and execute product retirement. End-of-life triggers, retirement
  planning, successor path, and decommission sequence. Use when a system may
  no longer serve its purpose.
model: sonnet
---

# Retirement

Use this skill when a system, capability, or workflow may be approaching
end-of-life.

## Current Project Context

### Retirement Plan
!`cat docs/07-retirement/retirement-plan.md 2>/dev/null || echo "No retirement plan found."`

### Product Definition Summary
!`cat docs/01-project-definition/project-definition-summary.md 2>/dev/null || echo "No product definition summary found."`

## Interaction Pattern

1. Evaluate end-of-life triggers.
   - Does a replacement exist?
   - Has usage fallen below a meaningful threshold?
   - Does maintenance consistently exceed delivered value?
   - Is a key dependency reaching end-of-life?

2. Re-engage solution alignment thinking.
   - Ask whether maintaining this system still advances an active goal.

3. If retirement is justified, plan the transition.
   - Identify dependents.
   - Define the successor or exit path.
   - Set timeline and communication expectations.

4. If data must move, verify export before announcing end-of-life.
   - Do not rely on assumed migration paths.

5. Guide the decommission sequence.
   - Announce -> Migrate or Export -> Disable or Archive -> Notify

## Outputs

Produce or update these artifacts as needed:

- `docs/07-retirement/retirement-plan.md`

Use the template in this skill directory when scaffolding or substantially
reshaping the retirement artifact.
