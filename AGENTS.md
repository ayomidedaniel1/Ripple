# Ripple - Agent Instructions

You are implementing **Ripple**, a visual spreadsheet debugger and scenario explorer, in this repository. Ripple answers: **"If I change this cell, what else changes, and why?"**

Read `README.md`, this file, `PROMPTS.md`, and `STATUS.md` (if present) before changing code. The user will issue prompts sequentially; **complete only the current prompt**. Never preemptively implement future milestones.

## Non-negotiable product intent
- Use **real Univer SDK** to edit, calculate, snapshot, and restore spreadsheets. Do not simulate spreadsheet functionality with HTML tables or hardcoded calculations.
- The **dependency explorer** maps formula inputs and their downstream dependents, distinguishes supported/unsupported formulas, and navigates to the associated cells.
- **Impact analysis** compares captured baseline results against recalculated results and explains *observed* changes, never guessed values.
- **Scenarios** are isolated, reversible experiments. Discarding an experiment cannot mutate the original workbook.
- **Impact Canvas** visualizes selected real workbook results. It is not a generic no-code dashboard builder.
- A seed/demo workbook must demonstrate these flows without external data or sign-in.
- Ship no AI chat, imported-file promises, real-time collaboration, or other unapproved V1 scope.

## Baseline architecture
- `apps/web`: Vite, React, TypeScript in strict mode, TanStack Router, TanStack Query, Tailwind CSS, accessible component primitives, React Flow (`@xyflow/react`), Recharts, Motion, Univer Sheets Core preset.
- `apps/api`: Java 25 LTS **if installed and compatible**; otherwise a supported installed Java LTS (21+) documented in `STATUS.md`. Spring Boot, Gradle wrapper, Spring Web, Validation, Spring Data JPA, PostgreSQL, Flyway, Spring Security when auth is in scope, OpenAPI.
- Local development: Docker Compose for PostgreSQL; `.env.example` for configuration; Java API is authoritative for persisted projects, revisions, and scenarios.
- Prefer `pnpm` workspaces for web. Do not add a Node backend, TanStack Start server runtime, Redis, LLM service, Kubernetes, or commercial Univer features in V1 without an evidence-backed need.
- Generate TS API contracts from OpenAPI when feasible; avoid duplicated untyped request shapes.
- Prefer Java records for immutable transport DTOs, constructor injection, service interfaces only where justified, migrations for every database schema change, and no Lombok by default.
- Prefer TypeScript function declarations and descriptive type names; use `Array<Type>` instead of `Type[]` where reasonable. No `any`, unless the API requires it and documented with validation.

## SDK and version discipline
1. Verify **current** official Univer documentation and installed package versions before coding. Starting point: https://docs.univer.ai/guides/sheets/getting-started/integrations/react . Typical packages: `@univerjs/presets`, `@univerjs/preset-sheets-core`. API signatures may change.
2. Initialize Univer once per mounted editor; dispose properly; avoid React rerender/remount loops and leaking observers. Use real SDK workbook/snapshot APIs.
3. Establish **confirmed SDK capabilities** using a narrow spike and type-safe code. Do not assume a public dependency-graph API exists. Evaluate actual public Facade APIs and formula-engine data before designing a custom analyser.
4. Never reimplement the formula engine in Java or TypeScript. A graph index is allowed for dependency analysis, but numbers and formula evaluations must come from Univer.
5. Dependency correctness: resolve relative/absolute refs, quoted sheet names, ranges, and cross-sheet refs within the advertised supported subset; explicitly mark `INDIRECT`, dynamic arrays, volatile functions, external links, and other unsupported expressions as incomplete until tested. Avoid false claims of completeness.
6. Changes may be asynchronous. Wait for calculation completion or otherwise establish stable results before publishing diffs. Handle rapid edits without stale comparison results.
7. Keep snapshot and scenario data typed, versioned, bounded, validated, and backed by integration tests. Consider workbook size, large payload limits, and optimistic concurrency.
8. Verify open-source vs. separately licensed features before adding a Univer package. Do not depend on paid charts, collaboration, or import/export for V1.

## Design direction
Inspiration, **not a clone**:
- Knotch: https://knotch.framer.ai/
- Reflex services: https://reflex-template.framer.ai/services
Use local browser/automation/design skills when available to inspect both visually. Record which skills/tools were actually available and used. Do not claim local installed skills exist without discovery. If a site cannot be inspected, explain and work from verified references and the product brief.

Create a recognizable, modern **product UI** rather than an agency landing page:
- Editorial typography with strong hierarchy, generous whitespace, disciplined grid, dramatic but restrained dark surfaces, crisp borders, refined contrast, subtle gradients and a single vivid accent family.
- Distinctive interaction: edited cell -> graph highlights affected path -> before/after metrics animate -> pinned Impact Canvas updates.
- Production-quality empty/loading/error states, focus styles, keyboard access, responsive layouts, reduced-motion preference, deliberate skeletons, useful command and context menus.
- Separate marketing surface from dense investigation workspace. Visual effects must not impair readability, spreadsheet performance, input latency, or accessibility.
- Reference sites provide ideas about visual rhythm, polished cards, premium section transitions, and live components; do not copy artwork, text, branding, layouts verbatim, or proprietary code.
- Preserve native Univer keyboard behavior and text selection. Constrain animation to surrounding UI, not thousands of grid cells.

## Development protocol for each user prompt
1. Inspect repository state, relevant code, `git status`, dependencies, installed tools, and existing tests. Never assume previous milestone was completed just because docs say it was.
2. Read `STATUS.md`, list acceptance criteria for *this* prompt, and identify hidden risks. Make the smallest viable plan.
3. Implement end-to-end, including API/database integration where relevant. No fake buttons, fake APIs, static mocks disguised as real production flows, or premature green checkmarks.
4. Add/adjust tests *in the same milestone*. Prefer a failing regression test first for bug fixes.
5. Run targeted tests, relevant builds/typechecking/lint, and real browser verification for UI tasks. If browser automation is unavailable, clearly label visual verification as unverified and give precise manual steps.
6. Update `STATUS.md`: completed, partial, unverified, known bugs, commands run with outcomes, any deviations, exact next prompt.
7. Explain what changed, what was genuinely tested, and remaining risk. Do not claim tests passed unless they executed.
8. STOP. Await the user's next prompt. Do not run ahead.

## Mandatory Java learning support
The owner is an experienced TypeScript/React engineer who is **learning Java while building Ripple**. Produce practical education without turning implementation into lectures:
- For each Java milestone, end with `Java learning notes` covering: **concept introduced**, **files/classes to read**, **TS/Node comparison**, **how the request flows through controller -> service -> repository -> database**, and **one 5-10 minute local exercise**.
- Write idiomatic, comprehensible Java with descriptive names, small classes and explicit types. Explain annotations and Spring magic when introduced.
- Where possible, show one working curl/HTTP example and its corresponding JUnit or Testcontainers test.
- Teach from *actual committed code*, not generic tutorials. Don't change code purely to illustrate a lesson.

## Verification and quality gates
- Backend: Gradle tests, JUnit 5, HTTP contract/integration tests, Testcontainers with PostgreSQL, Flyway validation; negative/validation/not-found/conflict tests; avoid H2-only confidence.
- Frontend: Vitest + Testing Library for logic and UI; Playwright for real browser flows. Verify strict TypeScript and production build.
- Product: workbook save/reopen, dependency graph, cross-sheet links, change->recalculation->diff, scenario isolation, discard, undo/redo, pinned canvas, persistence after reload, zero-data and failure cases.
- Avoid flaky sleeps in tests; wait on meaningful events and deterministic assertions. Do not run destructive operations against non-local environments.
- Use accessibility checks and screenshots at desktop and smaller viewport sizes. No animation-only state indicators.
- Tests must cover both core intended use and adverse paths. Any incomplete milestone must be marked incomplete.

## Security and durability
- Do not commit secrets. Provide `.env.example` and documented local defaults, configure CORS narrowly, validate request sizes, project ownership (when auth exists), and identifiers.
- Avoid arbitrary formula execution on the server. Do not interpret untrusted workbook contents as code. Sanitize user-supplied text displayed in graphs, tooltips, and exports.
- For scenario persistence, support idempotent operations when feasible and optimistic concurrency or clear conflict semantics.
- Avoid collecting analytics or uploading workbook contents to external services without explicit approval.

## Working agreement
- User is the final decision maker. Make reasonable documented implementation choices when not blocked; ask only about genuinely irreversible choices (e.g., introducing a commercial license).
- Do not claim local skills, screenshot review, deploy status, commits, or PRs without evidence.
- Never force-push, delete unrelated files, or overwrite existing user changes.
- The immediate objective is **a reliable, demonstrable Ripple MVP**. Contribution work in the upstream Univer repository and the later Tracewise project are out of current scope.
