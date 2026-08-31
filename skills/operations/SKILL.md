---
name: operations
description: >
  Production readiness and operations guidance. Readiness checklist,
  user-manual maintenance, incident lifecycle, and RCCA. Use before first
  release or when managing production systems.
model: sonnet
---

# Operations

Use this skill for live-system readiness and ongoing operational handling.

## Current Project Context

### Production Readiness
!`cat docs/06-operations/production-readiness.md 2>/dev/null || echo "No production readiness artifact found."`

### User Manual
!`cat docs/06-operations/user-manual.md 2>/dev/null || cat docs/06-operations/runbook.md 2>/dev/null || echo "No user manual found. It should have been created during design; see the design-review skill."`

### Recent Incident Records
!`find docs/06-operations/incidents -name "*.md" | sort 2>/dev/null || echo "No incident records found."`

## Interaction Pattern

1. Before first release, present the production readiness checklist.
   - Monitoring and alerting: are key signals defined?
   - User Manual: does it exist from Stage 3, and has Operations extended it with the machine-facing material — deployment, restart, rollback, on-call? Operations maintains this document; it does not author it at go-live.
   - Keep futures out of the manual. It describes present behaviour, because an operator acts on it directly. Anything designed but not yet shipped stays in the marked `<!-- UNSHIPPED: R-NNN -->` block that the release's closure step removes, so nobody follows a procedure the product will not perform.
   - Rollback plan: is there a tested path to restore prior state?
   - Operational ownership: is responsibility explicit?

2. For steady-state operations, guide the incident lifecycle.
   - Detect -> Triage -> Respond -> Resolve -> RCCA

3. Route RCCA findings back to the right stage.
   - Analysis for flawed assumptions or goals
   - Design for structural gaps
   - Implementation for code defects
   - Operations for process, monitoring, or configuration gaps

4. Route resulting bugs and enhancements back into the normal change pipeline.
   - Operations should not become an untracked side channel for product change.

## Outputs

Produce or update these artifacts as needed:

- `docs/06-operations/production-readiness.md`
- `docs/06-operations/user-manual.md` — extend and maintain; it is created in Stage 3
- `docs/06-operations/incidents/incident-*.md`

Use the templates in this skill directory when scaffolding or substantially
reshaping operational artifacts.
