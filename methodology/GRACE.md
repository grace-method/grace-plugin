# GRACE — Methodology Loaded Into Every Conversation

<!--
Managed by GRACE — overwritten on every /grace:install run.
This file is owned by the GRACE methodology. Do not edit it directly.
If you want to experiment with methodology changes, edit your fork of the
GRACE plugin source instead, and expect this file to be overwritten on
the next /grace:install.
-->

This file is the GRACE methodology entry point. It is loaded into every conversation in this project because your `.claude/CLAUDE.md` contains an `@.claude/GRACE.md` anchor line.

The methodology rules are loaded next via the imports below.

## Methodology Rules

@rules/principles.md

@rules/consultant-reflex.md

@rules/interaction.md

@rules/implementation-discipline.md

@rules/test-discipline.md

@rules/version-control.md

## Project Type

GRACE projects are classified into four types. The rigor of methodology work scales with the type.

| Type | Meaning | Default rigor |
|---|---|---|
| `a` | Personal learning or scratch work | Light |
| `b` | Personal tool or workflow | Light to medium |
| `c` | Tooling or methodology for other people or teams | Medium to high |
| `d` | Commercial, regulated, or mission-critical system | High |

The project's type is recorded in its own `.claude/CLAUDE.md` (the user-owned file) when the project is initialized via `/grace:project-start` or `/grace:project-setup`.

## Change Scale

Every change request should be classified before implementation begins.

| Scale | Typical examples | Default route |
|---|---|---|
| `trivial` | typo, rename, constant, wording fix | implement directly |
| `small` | simple bug fix, field addition, bounded behavior tweak | sniff test, then implement |
| `medium` | new feature, meaningful behavior change, new integration | do business analysis before implementation |
| `large` | cross-cutting change, new subsystem, architecture shift | do full business analysis and design review first |

## Decision Tree

- New initiative or unclear scope → use `/grace:project-start`; if unavailable, use `/grace:project-setup` first, then `/grace:project-definition` for Stage 1 Product Definition
- Release, phase, or delivery-slice selection → use `/grace:release-planning`
- Non-trivial work without approved analysis → use `/grace:business-analysis`
- Approved analysis without explicit design → use `/grace:design-review` or `/grace:testing-strategy`
- Implementation request → use `/grace:change-assessment` first, then `/grace:implementation`
- Validation or outcome review → use `/grace:validation`
- Operations or retirement work → use `/grace:operations` or `/grace:retirement`
- Risk reasoning at any stage → use `/grace:risk-management`

Slash command names assume the GRACE plugin is installed under its default name (`grace`). Skill names without the `/grace:` prefix (e.g. `project-start`) are equivalent when referenced inside other GRACE skills.

## File Ownership Recap

- `.claude/CLAUDE.md` is **yours**. GRACE only ensures one anchor line (`@.claude/GRACE.md`) is present in it; everything else is left alone.
- `.claude/GRACE.md` (this file) and `.claude/rules/*.md` are **owned by GRACE**. They are overwritten on every `/grace:install`. Do not edit them; your changes will be lost on the next install.

If you need to extend the methodology for a single project, add your extensions to `.claude/CLAUDE.md`, not here.
