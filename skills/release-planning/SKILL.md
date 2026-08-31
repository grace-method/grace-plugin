---
name: release-planning
description: >
  Define or revise a release, phase, or delivery slice. Selects a coherent
  product-graph focus, often from solutions but also from requirements, risks,
  defects, validation findings, or design options, then scopes the stage work
  needed to deliver it.
model: opus
---

# Release Planning

Use this skill when the user wants to define the next release, choose what to
build next, implement one or more approved solutions, revise release scope, or
turn a roadmap idea into a scoped delivery plan.

## Purpose

Release planning is a cross-stage discipline. It selects a coherent slice of
the approved product graph for near-term delivery. The lifecycle stages still
govern the quality of work; the release plan governs which work is in focus now.

## Inputs

Prefer current project artifacts over conversation memory:

- `docs/01-project-definition/`
- `docs/02-analysis/business-analysis.md`
- `docs/02-analysis/requirements/requirements.md`
- `docs/02-analysis/defensibility-report.md`
- `docs/03-design/`
- `docs/04-implementation/features/`
- `docs/05-validation/`
- `docs/06-operations/`
- `docs/BACKLOG.md`

## Interaction Pattern

1. Identify the release trigger.
   - solution or solution set
   - requirement subset
   - risk or mitigation
   - defect, validation finding, or incident
   - design option or prototype
   - external milestone or operational need

2. Select the focus nodes.
   - Name the primary graph nodes in scope.
   - Trace each to relevant goals, problems, requirements, risks, and existing design or implementation artifacts.
   - Flag orphaned or weakly traced scope before accepting it into the release.

3. Define release intent and boundaries.
   - State the release objective in outcome terms.
   - Record why this release matters now.
   - Separate in-scope, out-of-scope, deferred, and explicitly rejected work.

4. Classify in-scope solutions on the build-vs-buy spectrum.
   - For each in-scope solution, choose Adopt / Configure / Extend / Build
     per the Solution Types taxonomy in `grace-lifecycle.md`.
   - Prefer the leftmost viable option. Explain why a more rightward option
     is justified when used.
   - Classification is a release-time decision, not a property of the
     solution definition. The same solution may take different posture in
     different releases as substrate (vendors, platforms, tools) evolves.
   - The chosen posture determines the downstream activity profile (vendor
     evaluation, platform configuration, custom development) reflected in
     the work breakdown below.

5. Create the release-to-stage work breakdown.
   - Analysis: what must be confirmed, refined, or added?
   - Design: what design decisions, prototypes, or reviews are needed?
   - Implementation: what features, stories, or code changes are expected?
   - Validation: what evidence will show the release worked?
   - Operations: what readiness, user-manual, deployment, or support work is needed?
   - Retirement: what old capability, artifact, or process must be closed, if any?

6. Identify risks, dependencies, and assumptions.
   - Include scope risk, sequencing risk, technical risk, artifact drift risk, and human-review risk where relevant.
   - Prefer mitigation plans over vague risk labels.

7. Write or update the release plan.
   - Use `docs/release-planning/`.
   - Use `templates/release-plan.md` as the starting shape when creating a new release plan.
   - Number the plan at creation. Take the next free `R#` when you write the
     file, and name the file `release-NNN-slug.md` from the start. No release
     plan is ever renamed, so every reference to it is correct from the first
     one. Roadmap candidates — hypotheses with no plan document — stay
     unnumbered. Closed releases retain their numbers as historical artifacts.
   - A deferred, reordered, or dropped plan keeps its number. That is the
     accepted cost: gaps are cheaper than renaming, because stamping an
     identity does not propagate it to what already points at the old name.
   - Keep the plan concise enough to guide work without becoming a second copy of every stage artifact.
   - Write the Validation Guide as a human-performed walk-through, distinct from
     Success Criteria. Success Criteria states the bar; the Validation Guide is a
     short, interactive set of steps at the user's altitude (open the product,
     use the feature), each formatted as action → expected result → pass/fail
     checkbox. It is a second check that does not rely on the AI self-reporting
     that it validated. Include at least one step that demonstrates the relevant
     outer-loop / end-to-end test exists and passes (see `test-discipline.md`),
     so the human can directly catch the "unit tests pass but the product is
     broken" failure mode. Include at least one step drawn from the operator's
     calendar in the User Manual, preferring a rare one — first run, period-end,
     or recovery — since ordinary use never rehearses those and the release is
     the last cheap chance to find them broken.

8. Present for human review.
   - Do not treat the release scope as approved until the human accepts it.
   - After approval, route to the needed stage skills.

9. Close the release when its work is done.
   - **Close the plan first.** Set `Status` to `Closed <date>` and set every
     Stage Status row to its final value. The plan is the source of truth
     every later step compares against. If the plan declares joint closure
     with a sibling release, close the sibling in the same commit — otherwise
     that sentence is false the moment you write this one.
   - **Run the reference check.** A link or anchor that no longer resolves
     fails the test suite. Fix what it names before continuing. The usual
     cause at closure is the next step: an entry moved to an archive while
     its inbound anchors stayed behind.
   - **Walk the residue list** — the artifact classes whose own job is to
     state the release's state, each of which has an owner-step that should
     already have run. Verify rather than repair: decision records stamped
     dated at the design gate; the marked unshipped block in the user manual
     removed or promoted; entries moved to the archive **with their inbound
     references rewritten AND their own outbound bare `#anchor` links
     repointed, both in the move's commit** — a moved entry's links to
     entries that did *not* move break too, which is easy to miss because
     they still resolve inside the file they came from; the release index
     row; the acceptance decision carrying its date and its decider.
     Everything else
     abstains — it points at the plan or carries a date — so it is not on
     this list and does not need checking.
   - **Rank whatever is left** by whether a reader would act on it:
     operator-facing procedure text first, planning and design artifacts
     next, analysis records last.
   - **Ask the closing question:** did anything need re-tensing that is not
     on the residue list? If yes, fix it and add the row.
   - **File anything deferred**, citing the identifier in the sentence that
     defers it.
   - **Do not touch these**, all of which read as stale state and are not:
     dated records (a banner reading "reviewed 2026-08-28" is correct as of
     its date, and rewriting it destroys the record of what was known when);
     superseded-value clauses that deliberately keep a prior value;
     deliberately quoted stale wording held as evidence; and claims about a
     different work item that merely share a line with the release
     identifier.

## Output

Produce or update a release plan containing:

- release name and status
- release objective
- selected focus nodes
- rationale
- scope boundaries
- release-to-stage work breakdown
- risks, dependencies, and assumptions
- success criteria
- validation guide (human-performed; distinct from success criteria)
- next routing decision

At closure, produce the closure record: the plan set to `Closed`, the residue
list verified, the reference check passing, and any deferrals filed by
identifier.

## Gate

Human approval is required before treating the release plan as the active scope
for downstream design, implementation, validation, or operations work.
