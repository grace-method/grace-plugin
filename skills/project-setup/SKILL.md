---
name: project-setup
description: >
  Initialize a new project or initiative. Establish artifact strategy,
  classify project type, set predictability expectations, and scaffold the
  methodology starter structure before Stage 1 product definition begins.
model: opus
---

# Project Setup

Use this skill at the very start of a new project or when introducing GRACE
into an existing repository that does not yet have a clear artifact strategy.

## Purpose

This skill exists to make the Stage 1 entry path real. It does not perform full
product definition or business analysis. Its job is to:

- classify the project at a high level
- choose how the methodology will map onto the repository
- scaffold the core artifact structure and starter orientation files when appropriate
- make the artifact strategy explicit before downstream work begins

## Interaction Pattern

1. Classify the project type.
   - `a` — personal learning or scratch work
   - `b` — personal tool or workflow
   - `c` — tooling or methodology for others or teams
   - `d` — commercial, regulated, or mission-critical system

2. Set the predictability dial.
   - `high` — work is expected to be governed, review-heavy, and artifact-forward
   - `low` — work is exploratory and lighter-weight
   - `hybrid` — early work is exploratory, but later stages need stronger structure

3. Choose the artifact strategy.
   - **Standard structure:** create the GRACE `docs/` tree in the repository
   - **Custom mapping:** keep the org's existing structure and map GRACE concepts onto it

4. If standard structure is chosen, scaffold the folder tree and seed files.
   - Create only missing files by default.
   - Seed only the starter orientation files unless the human explicitly asks for more.
   - Let stage skills create detailed stage artifacts when that stage begins.
   - Do not overwrite existing artifacts unless the human explicitly asks for it.

5. Establish environment parity.
   - Identify all environments the project will run in (local, preview, production, CI).
   - Pin the runtime version explicitly: `engines` field in `package.json`, `.nvmrc`, or equivalent for the stack.
   - Confirm the same version will be used in all environments.
   - Flag any known environment differences as intentional technical debt.

6. Present the artifact strategy statement for approval.
   - Summarize project type, predictability dial, chosen structure, and any important exceptions.

## Standard Structure

When using the standard structure, scaffold this tree:

```text
docs/
├── README.md
├── HANDOFF.md
├── BACKLOG.md
├── release-planning/
├── 01-project-definition/
├── 02-analysis/
│   └── requirements/
├── 03-design/
│   └── adr/
├── 04-implementation/
│   └── features/
│       └── stories/
├── 05-validation/
├── 06-operations/
│   └── incidents/
└── 07-retirement/
```

## Template Sources

Use these sources when seeding starter files:

| Target path | Template source |
|---|---|
| `docs/README.md` | `templates/docs-readme.md` |
| `docs/HANDOFF.md` | `templates/handoff.md` |
| `docs/BACKLOG.md` | `templates/backlog.md` |

## Current Implementation Note

Stage 1 template ownership lives with `project-definition`, and the rest of the
lifecycle templates live with their corresponding stage skills. `project-setup`
owns the structure, starter orientation files, and routing decision, not the
detailed stage artifact content.

## Output

Produce a short project-setup record containing:

- project type
- predictability dial
- chosen artifact strategy
- scaffold action taken or deferred
- important mapping notes or exceptions

## Gate

Human approval is required before treating the artifact strategy as settled or
proceeding into product definition or business analysis.
