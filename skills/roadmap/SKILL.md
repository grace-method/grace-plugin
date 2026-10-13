---
name: roadmap
description: >
  Create, read, or update the project roadmap — the whole shape of the product
  at one level above any single release. Use when starting or closing a release,
  when reviewing at the product's operating cadence, when someone returns after
  a gap and needs to reorient, or when asked what to work on next.
model: opus
---

# Roadmap

Use this skill when the human wants to know where the project stands and what is
most valuable next, when a release is about to start or has just closed, when the
product's operating cadence calls for a review, or when a returning reader needs
to reorient after a gap.

## Purpose

The roadmap is the product's whole shape at one level above any single release:
where the work has been, where it may go, and what is finished. **A release plan
elaborates one block of the roadmap; the roadmap never elaborates itself into
one.**

That boundary is the guard against a second, competing planning capability. The
moment a roadmap starts carrying scope boundaries, work breakdowns, or success
criteria, it has become a release plan and the shape it existed to show is gone.
Those belong in `docs/release-planning/`; refuse to write them here.

**One shape serves every reader.** The two ways a roadmap is read are a sequence,
not a choice: a reader establishes what is true now, then either continues into
the plan of a release in flight, or decides what is most valuable next. Do not
ask the human which kind of roadmap they want — write the one that serves both.

## Inputs

Prefer current project artifacts over conversation memory.

- `docs/ROADMAP.md` — if it exists, **read it before writing anything**
- `docs/01-project-definition/` — the strategic goal and intended value
- `docs/release-planning/` — each plan is the **source of truth for its own status**
- `docs/02-analysis/business-analysis.md` — goals candidates are scored against
- `docs/BACKLOG.md` — where candidates are drawn from
- `git log` — what actually happened, when the record is thin

## Interaction Pattern

### A. Creating the roadmap

Created at the close of Stage 1, the first moment a strategic goal and intended
value exist. Earlier is an empty template masquerading as evidence.

1. **State the strategic goal.** One sentence, the question the project is judged
   on, taken from the product definition. Not restated analysis — the single bar.

2. **Write the orientation block.** What is true *now*, in a form a cold reader
   can absorb in two minutes: what is delivered, what is in flight, what is not
   started. Prefer a small table of measured facts over prose. Measure them; do
   not recall them.

3. **Lay out the sequence.** Releases and candidates in order, with dependencies.
   Say which ones everything else waits on and which are independent — a reader
   deciding what to do next needs to know what is actually blocked.

4. **Give every candidate its value rationale.** The goal it serves and why the
   ordering is what it is. A roadmap that lists candidates without this cannot
   answer *what is most valuable next*, which is half its job. Do **not** score
   against a budget or a walk-away threshold unless the human asks; whether to
   continue is theirs, not the document's.

5. **Record what is explicitly not planned.** Non-goals carried from Stage 1.
   These stop settled questions being reopened by every returning reader.

6. **Record the currency triggers in the document itself**, so the next reader
   knows what keeps it true.

7. **Make it reachable.** Writing the file is half the fix. Check that the entry
   points a returning reader actually uses — `docs/README.md`, any resume prompt,
   the release-planning index — name it. Then check that no other artifact
   *restates* it: a summary living elsewhere actively prevents the original being
   found, because the reader finds the copy and never learns the original exists.

### B. Reading it (reorientation, or deciding what is next)

8. **Report the orientation block first**, then route: if a release is in flight,
   point at its plan and stop. If none is, present the candidates with their value
   rationale and let the human choose.

9. **Verify before reporting.** Check the roadmap's dated claims against the
   release plans and `git log`. If the document disagrees with reality, say so
   plainly and offer to update it — never relay a stale claim as current.

### C. Updating it

Three triggers, and they answer different failures.

| Trigger | Fires when | Answers |
|---|---|---|
| **Release closure** | A release closes — in the closing commit | Internal staleness |
| **Release start** | Before a release is planned | Scope drift away from the roadmap |
| **Operating cadence** | The rhythm in the User Manual's calendar | External drift |

10. **On closure**, update the release's dated status row. This is a residue item
    verified during `release-planning` closure, not a separate errand.

11. **On release start**, consult before planning: does this work appear in the
    roadmap? If not, either add it as a candidate or treat its absence as a signal
    that the roadmap has stopped describing the project.

12. **On cadence review**, ask what the closure trigger cannot: has the world
    moved? Internal staleness is prevented by step 10; **external drift can only be
    noticed**, and this is where. A missed cadence review is itself an attrition
    signal worth naming to the human.

13. **Date every status replica.** `Active since 2026-08-13`, never `Active`. The
    roadmap restates status the release plan owns, which is *state belongs to its
    owner* applied rather than waived — the replica is dated, and the moment that
    changes it is a named step. A dated replica announces its own staleness; an
    undated one lies quietly.

## Output

Create or update `docs/ROADMAP.md` containing: the orientation block, the
strategic goal, the release sequence with dependencies, dated status per release,
what has been delivered, what is next and why, candidates with the goal each
serves, explicit non-goals, and the currency triggers.

Use `templates/roadmap.md` as the starting shape. Scale length to the project —
fifteen lines is a legitimate roadmap — but keep the sections.

## Gate

Human approval is required before treating a roadmap's sequence as the agreed
direction. The roadmap states direction, not commitment: candidates are
hypotheses, and `release-planning` is free to disagree with them when it makes a
real plan.
