---
name: install
description: >
  Write GRACE methodology rules into the current project. Copies GRACE.md and
  the six rule files into .claude/, ensures the anchor line is present in
  CLAUDE.md, and records the installed methodology version. Safe to re-run
  after a plugin update. Use when setting up a new project with GRACE or when
  refreshing methodology files after an update.
model: opus
---

# Install GRACE Methodology Rules

This skill writes the GRACE methodology files into the current project so they
are loaded into every conversation via the @-import chain. It is the
counterpart to the plugin's skill distribution: skills travel with the plugin
automatically, but rules require on-disk files because plugins cannot ship
always-on CLAUDE.md content.

## What This Skill Does

Follow these steps in order. Do not skip steps. Report what you did at the end.

### Step 1 — Create directories

Create `.claude/` and `.claude/rules/` if they do not already exist.

### Step 2 — Write GRACE.md

Read the file `methodology/GRACE.md` from the plugin root (the directory
that contains this skill's parent `skills/` folder — go up two levels from
this SKILL.md to reach the plugin root, then into `methodology/GRACE.md`).

Write its contents to `.claude/GRACE.md` in the current project, overwriting
any existing file. This file is owned by the methodology; do not prompt
for confirmation before overwriting.

### Step 3 — Write rule files

Read each of the six rule files from `methodology/rules/` in the plugin root:
- `principles.md`
- `consultant-reflex.md`
- `interaction.md`
- `implementation-discipline.md`
- `test-discipline.md`
- `version-control.md`

Write each to `.claude/rules/<filename>` in the current project, overwriting
any existing file. These files are owned by the methodology; do not prompt
for confirmation before overwriting.

### Step 4 — Ensure anchor line in CLAUDE.md

The anchor line is: `@.claude/GRACE.md`

Check `.claude/CLAUDE.md` in the current project:

- **If the file does not exist:** Create it containing only the anchor line.
- **If the file exists and already contains the anchor line** (a line matching
  the regex `^@\.claude/GRACE\.md\b`): Do nothing to the file.
- **If the file exists and does NOT contain the anchor line:** Append the
  anchor line at the end of the file, preceded by a blank line.
- **If the file contains a near-match** (a line containing `GRACE.md` in an
  `@` import but not matching the exact pattern above): Report the mismatch
  to the user and ask them to confirm before replacing it.

Never modify any other content in `.claude/CLAUDE.md`. That file is owned by
the user.

### Step 5 — Record methodology version

Read `methodology-freshness.json` from the plugin root. Write or overwrite
`.claude/grace-methodology-version` in the current project with a short
summary:

```
grace-methodology-version: <version from methodology-freshness.json>
installed: <current ISO timestamp>
revision: <repositoryRevision from methodology-freshness.json>
```

### Step 6 — Report

Print a one-paragraph summary of what was written, including:
- The number of files written (GRACE.md + 6 rules + version marker = 8 files).
- Whether the anchor line was already present or was added.
- A reminder that `.claude/GRACE.md` and `.claude/rules/*.md` are owned by
  GRACE and will be overwritten on the next `/grace:install` run.
- A reminder that the user's `.claude/CLAUDE.md` is their own; only the
  anchor line is managed.

## Important constraints

- Do not touch anything outside `.claude/` in the current project.
- Do not read or modify `.git/`, source files, build outputs, environment
  files, or any content outside `.claude/`.
- Do not delete any files. This skill only creates or overwrites the specific
  files listed above.
- The plugin root is the directory containing this skill file's parent
  `skills/` folder. Use relative path traversal from this SKILL.md location
  to find `methodology/` and `methodology-freshness.json`.
