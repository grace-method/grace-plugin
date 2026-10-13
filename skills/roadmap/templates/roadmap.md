# <Product> — Roadmap

Where this project has been and where it is going. **The whole shape, at one
level above any single release.** A release plan in `release-planning/`
elaborates one block of this document; this document never elaborates itself
into one.

**Written to be read cold.** The expected reader may be returning after a gap of
days or weeks. If they cannot learn what is finished, what is not, and what the
best next step is within a couple of minutes, this document has failed.

---

## Reorient in <n> minutes

**Status as of <YYYY-MM-DD>.** <Last activity, and whether any gap since is a
gap or progress.>

**The one-sentence position: <what is true now, stated plainly — including the
uncomfortable half>.**

| | |
|---|---|
| <Is the product actually in use?> | <measured answer> |
| <Key health check> | <measured answer, with any caveat about what it does not prove> |
| <Coverage / completeness measure> | <n of m> |

<Any warning a returning reader would otherwise misread — a green check that
does not mean what it appears to mean, a milestone that looks closer than it is.>

**What is next, and why:** <one sentence, linking to the section below.>

---

## The strategic goal

> **<The single question this project is judged on.>**

<One short paragraph on why that is the bar, and what deeper purpose it serves.
Taken from the product definition, not restated analysis.>

---

## The shape

<A diagram where the sequence has dependencies worth seeing; a list where it does
not. State which release everything else waits on, and which are independent —
a reader choosing what to do next needs to know what is actually blocked.>

---

## Releases

Status here is a **deliberate replica** of each release document, which remains
the source of truth. It exists because this is the one place the whole sequence
is visible at once. **Every replica carries the date it became true**, so a stale
entry announces itself.

| Release | Objective | Serves | Status |
|---|---|---|---|
| **<R1 — name>** | <outcome, one line> | <goal it serves> | **<State> since <YYYY-MM-DD>** — see `release-planning/<file>` |
| **<R2 — name>** | <outcome, one line> | <goal it serves> | Not started as of <YYYY-MM-DD> |

<Say whether later entries are committed or indicative. Candidates that are still
hypotheses stay unnumbered; number them only once the sequence has been chosen.>

---

## Where we've been

| When | What landed |
|---|---|
| <YYYY-MM-DD> | <what was delivered, and what it means> |

<One line on what the history shows — the pattern a reader should take from it.>

---

## What is next, and why

**<The next piece of work, named.>**

<Why this before the alternatives. This is where the roadmap earns its keep: a
reader who has been away needs the reasoning, not just the item. Give the goal it
serves and what it unblocks.>

<Then the order that follows it.>

---

## Candidates

<Unnumbered until chosen. Each with the goal it serves and the reason for its
position. Drawn from the backlog, validation findings, risks, or operational
friction — cite by identifier where one exists.>

---

## Explicitly not planned

<Non-goals carried from Stage 1 and not reopened. These stop settled questions
being re-litigated by every returning reader.>

---

## How this document stays current

1. **A release closes → this document is updated** in the same commit. The status
   replica above is why; closure is when that debt is paid.
2. **<The product's operating cadence> → review it.** The beat that recurs whether
   or not building is happening. This is where *external* drift gets noticed —
   the world moving, needs changing — which no closure trigger can catch.
3. **Before a release starts → consult it.** *"Does this work appear here?"* If
   not, add it as a candidate, or notice that the roadmap has stopped describing
   the project.

**The dated-replica rule.** Any status copied from elsewhere carries the date it
became true — `Active since <YYYY-MM-DD>`, not `Active`. Undated status is what
rots.
