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

### Step 0 — Resolve the payload root, or stop

Everything below reads from a **payload root**: a directory containing **both**
`methodology-freshness.json` **and** `methodology/`. Find it by trying these in
order, and take the first that has both:

1. **Two levels up from this `SKILL.md`.** This is the plugin layout — the
   directory holding the `skills/` folder this file sits in.
2. **`~/.grace/`.** This is the local-runtime layout, deposited by `./install.sh`
   on a machine that installs from source rather than the marketplace.

**If neither has both files, STOP.** Write nothing. Report every location you
tried and what was missing from each, and tell the user to run `./install.sh`
from the GRACE repository if this is a local-runtime machine.

**Do not search for a payload root, and do not use one you were not given by
this list.** An agent told to "find the plugin root" will search the filesystem,
find some checkout of the methodology repository, and write a project from a
source nobody verified — and that failure looks exactly like success. A skill
that stops can be recovered from; a skill that guesses cannot even be detected.
This is defect D-10, and Step 2 below is where it used to fail silently: on a
local-runtime machine two levels up resolves to `~/.claude/`, which holds only
`rules/` and `skills/`, so there was never a `methodology/GRACE.md` to read.

Refer to the directory you resolved here as **the payload root** for the rest of
this procedure.

### Step 1 — Create directories

Create `.claude/` and `.claude/rules/` if they do not already exist.

### Step 2 — Write GRACE.md

Read `methodology/GRACE.md` from the payload root resolved in Step 0.

Write its contents to `.claude/GRACE.md` in the current project, overwriting
any existing file. This file is owned by the methodology; do not prompt
for confirmation before overwriting.

### Step 3 — Write rule files

Read each of the six rule files from `methodology/rules/` in the payload root:
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

Read `methodology-freshness.json` from the payload root resolved in Step 0. Write or overwrite
`.claude/grace-methodology-version` in the current project with a short
summary:

```
grace-methodology-version: <version from methodology-freshness.json>
installed: <current ISO timestamp>
revision: <repositoryRevision from methodology-freshness.json>
provenance: <clean | DIRTY — revision does not describe installed content>
```

The `provenance` line is required. Read `workingTreeDirty` from
`methodology-freshness.json`: write `clean` when it is `false`, and
`DIRTY — revision does not describe installed content` when it is `true`.

When it is `true`, also tell the user plainly that the plugin was built from an
uncommitted tree, so the recorded revision names a real commit whose content is
not what was installed. Copying provenance known to be false is the defect this
line exists to prevent (D-01, requirement 8.9); a marker that looks valid and
is wrong is worse than one that admits it cannot be substantiated.

### Step 6 — Record what you wrote

Write two files into `.claude/`, describing exactly the seven methodology files
this procedure wrote — `GRACE.md` and the six rules. Not `CLAUDE.md`, which is
the user's, and not the version marker. Together they are what lets `./grace-runtime-status.sh`, run
from inside this project later, answer *"has this copy been edited since it was
installed?"* — a question that is otherwise unanswerable here, so a project
without them is reported as **UNVERIFIABLE** rather than as current.

**`.claude/.grace-installed`** — one path per line, relative to `.claude/`,
naming what GRACE owns in this project and nothing else:

```
GRACE.md
rules/principles.md
rules/consultant-reflex.md
rules/interaction.md
rules/implementation-discipline.md
rules/test-discipline.md
rules/version-control.md
```

**`.claude/.grace-installed.sha256`** — the same seven files with their hashes,
in `shasum -a 256` output format, generated after the writes above have
completed so it describes the bytes actually on disk.

**Derive it from the ownership list you just wrote, never from a glob:**

```sh
cd .claude && tr '\n' '\0' < .grace-installed | xargs -0 shasum -a 256 > .grace-installed.sha256
```

**Do not use `shasum -a 256 GRACE.md rules/*.md`.** That glob matches every `.md`
in `rules/`, including rules the user wrote themselves — putting a file GRACE
never wrote into GRACE's own content record. The moment the user edits their own
rule, the status tool reports *"deployed content differs from what this install
wrote"*, which is false in every word for that file, and the remedy it offers
re-runs this skill and launders their edit into GRACE's record. The machine layer
had exactly this bug and stopped using a glob over the shared rules directory for
exactly this reason. The invariant is in `install.sh`: **"No entry exists for a
file GRACE did not write."**

Neither file lists itself, and neither lists `CLAUDE.md` or
`grace-methodology-version`. The two files therefore have **the same number of
entries** — seven. If the receipt has more, a glob has crept back in. Ownership is **per file, never per directory**:
anything of the user's in `.claude/` is absent from both lists and is therefore
never touched by any later run.

### Step 7 — Report

Print a one-paragraph summary of what was written, including:
- The number of files written: GRACE.md + 6 rules + the version marker = 8,
  plus the two ownership records from Step 6, which describe the seven
  methodology files and not themselves.
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
- The payload root is whatever **Step 0** resolved, and only that. Do not
  re-derive it later in the procedure, and do not fall back to path traversal
  if a read fails — traversal is Step 0's first rung and the local runtime is
  its second. If Step 0 could not resolve a root, the procedure has already
  stopped.
