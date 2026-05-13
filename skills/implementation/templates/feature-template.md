# Feature-001: (title)

## Traceability
- **Business Analysis Solutions:** S-01, S-02
- **Requirements:** REQ-001, REQ-002

## Acceptance Criteria

<!-- Feature-level acceptance criteria describe cross-story, integration-level behaviors -
     things that emerge from multiple stories working together but are not owned by any one story.
     Each criterion maps to a smoke or end-to-end test.

     Write these in user-friendly language first. A non-technical reviewer should be able to
     understand what success looks like without knowing implementation details. Keep the
     technical precision in lower sections such as Test Mapping, Verification Notes, or the
     linked story docs.

     WRONG - single-component behavior (belongs at story level):
       Given a note is saved via notes-widget.js,
       when the content is empty,
       then the row is deleted.

     RIGHT - cross-story integration behavior:
       Given a user saves a note on a resource page in one tab,
       when they switch to course.html in another tab,
       then the note content is visible in the module's resource list.

     If a criterion could be moved to a single story without losing meaning,
     it belongs at the story level, not here. -->

- Given ... when ... then ...
- Given ... when ... then ...

## Test Mapping

<!-- Map feature-level criteria to smoke/e2e tests. This is where the testing strategy's
     outer-loop model meets concrete test cases. -->

| Criterion | Test Layer | Test Location |
|---|---|---|
| (criterion summary) | Smoke / E2E | `tests/smoke/(file)` |

## Stories

<!-- Each story has its own file in stories/ with story-level acceptance criteria and test mapping. -->

- [ ] [Story 1: (title)](stories/story-001.md)
- [ ] [Story 2: (title)](stories/story-002.md)

## Retrospective

<!-- Every feature should close with a retrospective once the implementation is working and the
     feature is effectively complete or at a meaningful checkpoint. The retrospective captures
     what the feature taught us: successful practices, painful surprises, key tradeoffs, testing
     gaps, and follow-up actions.

     Use [retrospective-template.md](./retrospective-template.md) as the starting point and place
     the completed retrospective alongside the feature doc, usually as `retrospective.md` inside
     the feature folder.

     Keep this artifact honest and useful. It should help a future contributor understand not only
     what shipped, but what was learned while shipping it. -->

- [ ] `retrospective.md` exists inside the feature folder and is based on `retrospective-template.md`

## Release Notes
<!-- Fill in as stories complete. Plain language - understandable to a non-technical stakeholder. -->

_In progress_

<!--
Example when complete:
Users can now browse the full cattle listing with photos, price, and breed details.
Listings are filterable by breed and age range. Addresses S-01 (searchable catalog) and S-02 (buyer self-service).
-->
