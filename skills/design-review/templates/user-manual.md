# User Manual — <Product>

<!-- The product's operating instructions, written for the person whose job this
     product is part of — not for a technical operator. Created in Stage 3
     (Design), before the code is finished: writing the steps is what exposes
     design defects while they are still cheap to fix. Refine it by successive
     passes as the product takes shape; do not wait for go-live.

     Two orderings live in this document on purpose:
       - "Your Calendar" is ordered by CADENCE — when things happen.
       - "How To" is ordered by TOPIC — look-up reference.
     Topic order dissolves the step-to-step adjacency the procedure walk exists
     to inspect, so the calendar cannot be folded into the reference. -->

## What This Is For
<!-- One short paragraph in the reader's own vocabulary. What job does this
     product do for them? -->

## Before You Start
<!-- First run only: what must exist, be installed, or be decided before the
     first procedure below can be performed. -->

---

## Your Calendar

<!-- REQUIRED, and ordered by cadence — not by topic.
     List every procedure the operator performs, including the rare ones: first
     run, period-end, year-end, recovery, and re-entry after a gap. A rare
     procedure is the one most likely to be missing here, and the most expensive
     to discover missing. -->

| When | Procedure | Roughly how long |
|---|---|---|
| Every day or week | | |
| Every month | | |
| Every quarter | | |
| Every year | | |
| Once, at setup | | |
| Only when something has gone wrong | | |

### <Procedure name>

**When:** <cadence or trigger>

**Before you begin, this should already be true:**
<!-- The state this procedure assumes, named concretely enough that the operator
     can check it. This is the line the walk tests: is this state something the
     PREVIOUS step actually leaves behind, and will the system accept it? -->
-

**Steps**
1.
2.

**When you are done, this is now true:**
<!-- The state this procedure leaves behind for whatever comes next. -->
-

**If it goes wrong**
<!-- The recovery path, in the operator's terms. -->
-

<!-- Repeat the block above for each procedure named in the calendar. -->

---

## How To
<!-- Topic-ordered reference: individual tasks the reader looks up when they have
     a question, as distinct from the procedures above, which they perform in
     order. -->

### <Task>

---

## Reference
<!-- Vocabulary, field meanings, file locations, conventions — whatever the
     reader needs to look up. -->

---

## Running the System
<!-- Added and maintained in Stage 6 (Operations), not at design time. The
     machine-facing material: deploy, restart, roll back, monitor, escalate.
     Keep it in this document so there is one place to look, but below the
     operator-facing material, because it is not what most readers came for. -->

### Deploy a new version
1.

### Roll back to the prior version
1.

### Restart
1.

### Check system health
1.

### Alerts

| Alert | What it means | First response |
|---|---|---|
| | | |

### Known Issues and Workarounds

| Issue | Workaround |
|---|---|
| | |

### Contacts

| Role | Name / Channel |
|---|---|
| Operational owner | |
| On-call | |
