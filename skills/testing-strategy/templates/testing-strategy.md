# Testing Strategy

<!-- This artifact is produced during Design (Stage 3) alongside the design package.
     It answers: "How will we verify this project's software works?"

     Not every project needs every layer. Include only what the project requires,
     and justify the choice. If this document is not influencing how tests are written,
     it is too abstract - revise it.

     The testing strategy is a project-level decision. The methodology does not
     prescribe specific layers or tools. Common layers include unit, contract,
     integration, and end-to-end/smoke, but the right set depends on the project. -->

## Test Layers

<!-- For each layer this project uses, define:
     - What it verifies
     - What tool runs it
     - When it runs
     - What it does not cover

     If a layer uses browser automation, document which browser/channel it runs
     against. Prefer isolated automation browsers over an installed daily-driver
     browser when possible. -->

### Layer: (name)

- **Purpose:** What this layer verifies.
- **Tool:** What runs these tests.
- **Runs when:** When in the development workflow these tests execute.
- **Does not cover:** What is explicitly outside this layer's responsibility.

---

### Layer: (name)

- **Purpose:**
- **Tool:**
- **Runs when:**
- **Does not cover:**

## Where Acceptance Criteria Live

<!-- Acceptance criteria do not belong in this document. The testing strategy defines
     layers and tools for the whole project. Criteria belong in:

     - Feature docs (04-implementation/features/) for cross-story, integration-level
       behaviors that map to smoke/e2e tests.
     - Story docs (04-implementation/features/stories/) for single-component behaviors
       that map to unit/contract tests.

     Each feature and story doc should have its own test mapping table that connects
     criteria to specific test files using the layers defined here. -->

## Test Execution Workflow

<!-- Describe the sequence a developer follows. Example:

     1. Inner-loop TDD: Run unit tests on each Red-Green-Refactor cycle
     2. Before commit: Run contract tests
     3. Before merge: Run full suite including smoke tests
     4. CI: All layers run on every push -->

1.
2.
3.

## Constraints and Tradeoffs

<!-- Document testing decisions that involve tradeoffs. Examples:
     - "We use a real database for integration tests (slower) because mocks masked a migration failure."
     - "Smoke tests require a running dev server, so they run pre-merge rather than on every save."
     - "Browser automation uses a bundled test browser instead of the installed consumer browser."
     - "We have no end-to-end tests because this is a CLI tool with no UI." -->

- 
