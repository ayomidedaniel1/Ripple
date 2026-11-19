# Ripple - Codex implementation runbook

Use these prompts **in order**, one per Codex turn, from the root of your Ripple repository. Copy `AGENTS.md`, `README.md`, and this file into the root **before** starting. Each prompt assumes previous milestones were verified, but tells the agent to inspect instead of assuming. Stop after each prompt to review the evidence and Java learning notes.

**Global execution rule:** `AGENTS.md` always applies. Do not run ahead. If a gate fails, fix within the same prompt before moving on. Use local tools/skills only if discovered on the PC; never fabricate test runs, browser inspections, skill availability, or screenshots.

---

## Prompt 01 - Repo reconnaissance, SDK feasibility, and actionable plan

```text
You are the implementation agent for Ripple. Read AGENTS.md, README.md, PROMPTS.md and STATUS.md (if present) fully.

Do not scaffold the app yet. Inspect the actual repository, git status, installed toolchains (Node, pnpm, Java, Docker, Gradle), available Codex/local skills and MCPs, and current official docs for Univer React/Vite integration, public formula APIs, workbook snapshots and editing events. Inspect the Univer version we can install and distinguish OSS from commercial capabilities. Research suitable integration boundaries for Java/Spring Boot and Vite/TanStack Router.

Create docs/FEASIBILITY.md with a capability matrix: verified public SDK API, experimental/internal API, unknown, missing, and suggested fallback, with doc/code references and version checked. Pay particular attention to dependency graph availability, cross-sheet formulas, result-settled signals, reading cell data, programmatic selection, undo/redo and snapshot save/restore. For any uncertain critical API, propose a minimal source/typecheck spike to execute in Prompt 04, rather than pretending it works.

Create docs/ARCHITECTURE.md with system components, ownership of calculations, persistence and scenario isolation, API boundaries, initial entity schema, sync/recalculation strategy, graph indexing limitations, error handling and test plan. Propose a build order that produces a runnable vertical slice quickly. Create STATUS.md with only observed repo state and concrete next actions.

For Java learning: include a 1-page Java/Spring glossary mapping controllers/services/repositories/records/DTOs to Node/TypeScript concepts, and what we will build first. Do not implement future milestones.

Verify that documentation links work where possible. Conclude with: decisions, unknowns, available local skills, commands run, exact next prompt and remaining risks. STOP.
```

**Gate:** Feasibility report distinguishes verified/unknown SDK APIs and OS licensing; repo status is factual. No false implementation claims.

## Prompt 02 - Design discovery, design system, and clickable UI specification

```text
Read project docs and STATUS.md, then inspect both design references using whatever browser/visual-design skills, Playwright/browser MCP, screenshot tools or UI audit skills are actually installed:
https://knotch.framer.ai/
https://reflex-template.framer.ai/services

First discover available local skills/MCPs in this Codex environment and read applicable SKILL.md/instructions. Record which were found and used. If a website or tool is inaccessible, state it and do not invent visual details. Also examine Univer's real UI constraints and keyboard interactions.

Analyze each reference for transferable design principles: typography, color, spacing, section rhythm, contrast, interactive cards, animations, responsiveness, polished visual storytelling. Do not clone any site's branding/assets/markup.

Create docs/DESIGN.md: Ripple's original visual identity, precise palette tokens (light/dark where useful), typography scale, grid and spacing tokens, component system, accessible interactions, empty/loading/error states, mobile/tablet behavior and reduced-motion plan. Specify 3 distinct product surfaces: marketing home, projects/demo entry, and the dense investigation workspace. The workspace must show workbook editor, formula selection context, dependency graph, change inspector, scenarios and Impact Canvas without overwhelming the user. Include wireframe descriptions for desktop and small screens, key interaction flows, and a prioritized component inventory. Aim for premium visual quality grounded in usefulness, not random glassmorphism or a generic dashboard.

Choose one direction, document why, and design an especially memorable 30-second demo: editing CAC in the demo workbook -> highlighted dependency paths -> profit/runway delta -> Impact Canvas updated -> discard scenario. Provide screenshots/evidence paths if captured. No implementation yet.

End with an explicit visual acceptance checklist the agent can evaluate later. Update STATUS.md. STOP.
```

**Gate:** Design decisions grounded in inspected references and product use cases; no site cloning; explicit screenshots when accessible.

## Prompt 03 - Runnable monorepo foundation and Java API health check

```text
Implement the initial runnable Ripple repository according to docs/ARCHITECTURE.md and docs/DESIGN.md. Inspect first. Use Vite + React + strict TypeScript + TanStack Router + Query, Tailwind and design tokens in apps/web. Use Spring Boot + Java (prefer installed Java 25 LTS when compatible; otherwise a supported Java LTS), Gradle wrapper, Spring Web, Validation and Actuator in apps/api. Setup Docker Compose PostgreSQL, Flyway dependency/config, .env.example, CORS for local frontend, and a documented local start workflow. Pin working compatible versions; no unnecessary extras.

Render real routed pages with usable navigation, not a full fake marketing site. Add a typed HTTP client and actual Java /api/health or Actuator health route plus a frontend connectivity indicator. Keep a minimal polished layout with correct empty/loading/error states.

Create automated tests: Java controller health integration test, frontend route smoke test. Verify Gradle tests, frontend typecheck/build/test, API start and browser-to-API request. Use browser automation if available and capture screenshot of desktop/mobile layout. Update README with actual verified commands and STATUS.md with evidence. Do not implement workbook features yet.

Java teaching: explain Spring Boot application bootstrap, @RestController, dependency injection, records and differences from Express; show a curl command, test file, and 10-minute exercise to add/verify an endpoint. STOP.
```

**Gate:** API and web start locally and talk to each other; reproducible tests/build; runnable instructions.

## Prompt 04 - Real Univer editor spike and demo workbook

```text
Read current project state and the SDK feasibility notes. Integrate the current *open-source Univer Sheets Core preset* into the actual React workspace. Check installed types and official docs before coding. Initialize/dispose only once per editor lifecycle; ensure editor has usable dimensions and no React StrictMode double-mount problems. Keep an adapter boundary around SDK calls with typed methods: initialize, load workbook, save snapshot, get/set selected cell or range, subscribe/unsubscribe to events where supported.

Create a believable multi-sheet demo financial workbook with inputs, formulas, money/percent formatting, cross-sheet references and multiple named business metrics (CAC, customers, revenue, profit, runway). Values must be calculated by Univer, not manually hardcoded. Render the native editor, allow editing, verify recalculation, undo/redo and snapshot round-trip. Build a small developer diagnostic panel (development only) to show selected address and observed formula/value to help future milestones.

Execute the minimum feasibility spikes noted in docs/FEASIBILITY.md and update the capability matrix with actual code/test evidence. Add tests for adapter logic and a browser E2E of editing a formula input and seeing the real computed result. If formula recalculation/events are async, await real readiness instead of sleeps. Do not build the graph yet.

Record exact SDK versions, known limitations, test outcomes and screenshots; update STATUS.md. Java learning: describe why calculation remains in the browser, the API's future responsibilities, and a small exercise identifying the responsibility of each layer. STOP.
```

**Gate:** Real Univer mounted; user can edit workbook, formulas actually recalculate, snapshot round-trips and cleanup works. No mock editor.

## Prompt 05 - Java persistence and the first complete save/reopen flow

```text
Inspect repo and implement real Java persistence for projects and workbook revisions. Design minimum PostgreSQL tables with Flyway migrations, JPA entities/repositories, typed REST DTOs, service layer, validation and clear error responses. Choose and document a compatible snapshot serialization approach: opaque JSONB workbook snapshots with schema/version metadata, size bounds and optional integrity checks, not a Java formula interpreter. Endpoints must support creating/listing/getting projects and saving/getting a revision with conflict semantics (optimistic version or explicit expected revision).

Wire the frontend to the *real* API via TanStack Query and typed contracts, replacing any temporary local-only save flow. Save the Univer snapshot, reload the page, reopen the saved project and verify data/formulas persisted correctly. Add save status and failure recovery UI. Never silently overwrite revisions.

Add Testcontainers PostgreSQL integration tests proving migration, CRUD, validation, 404, invalid payload, revision conflict, reopen. Add browser E2E save/reload. Run real tests, update OpenAPI/types, docs and STATUS.md.

Java learning: walk through one actual request from controller to service to repository to PostgreSQL and back, explain @Entity, @Transactional, Flyway and records vs TypeScript types. Include curl requests and a 10-minute task to implement/inspect a validation case. STOP.
```

**Gate:** True database-backed save/reopen and conflict/validation tests; frontend does not pretend data saved.

## Prompt 06 - Correct, bounded dependency graph index

```text
Study current Univer formula APIs/source and docs/FEASIBILITY.md before implementation. Design and implement a typed dependency indexing layer that derives formula precedents from *actual Univer workbook formulas*, not from hardcoded demo data. Prefer verified public SDK dependency APIs if adequate; otherwise use a well-tested parser/indexer for a precisely documented supported subset. Cover A1 references, absolute/mixed references, rectangles/ranges, multiple sheets, quoted sheet names, cycles and duplicate edges. Show node identifiers containing workbookId/sheetId/address, and track graph confidence/completeness per formula.

Do not claim dynamic arrays, INDIRECT, OFFSET, external workbook references, spills or named ranges are supported unless verified. Mark unknown/dynamic dependencies; never silently show an incomplete graph as comprehensive. Keep performance reasonable for medium-sized sheets: incremental rebuild where feasible, caps/debounce, and no rendering every cell as a node.

Create the Dependency Explorer UI with @xyflow/react: focused upstream/downstream graph, zoom/fit, path highlighting, selected cell details, keyboard navigation and click-to-select cell in Univer. Link to the selected cell and show references visibly.

Add deterministic unit tests for parser/indexer edge cases, cycles and cross-sheet refs; Playwright test with real workbook selection proving the graph's references. Verify builds. Update docs/limitations and STATUS.md. Java learning: short comparison of TS graph indexing vs a Java Map/Set implementation, with a tiny Java exercise (no scope creep). STOP.
```

**Gate:** Evidence-backed graph for documented subset, tests for ref semantics and honest unsupported markers.

## Prompt 07 - Accurate change impact engine and explainable diffs

```text
Inspect existing graph, Univer recalculation events and current tests. Implement a change impact engine that captures immutable baseline values/formulas, observes real workbook edits, waits until the relevant calculations settle, compares before/after results, and traces affected paths. Support batching, cancellation/version checks for rapid edits, deduplication, rollback, and changed cells that have no formula dependents. Distinguish 'may depend on' from 'value actually changed'. Use actual recalculated values, not arithmetic reimplemented in JS or Java.

Expose an Impact Inspector with before/after values, absolute/percentage differences where numerically valid, cell addresses, explanation based on dependency paths, formula error details and graph highlighting. Good copy and polished motion, with reduced-motion fallback. Add tests for rapid edits, async/stale results, no-op edits, negative numbers, zero denominators, errors, and undo/redo. Use a real Univer E2E: modify CAC, confirm dependent profit change and graph highlight, then undo and see baseline restored. Update STATUS.md and any API implications. Java learning: explain immutable snapshots/version counters and the analogous Java patterns; short exercise. STOP.
```

**Gate:** Impact matches actual workbook values and cannot show stale calculations after rapid updates.

## Prompt 08 - Isolated scenario playground, backend versioning

```text
Build the scenario lifecycle end-to-end. A scenario begins from a persisted immutable project revision. Users can branch, rename, edit assumptions safely, see deltas vs baseline, discard, and explicitly apply/commit a scenario. Verify the original workbook is not mutated by in-memory object aliasing or shared SDK state. Use workbook re-creation or a proven isolation strategy. Preserve scenario edits through refresh via Java REST endpoints, Flyway migrations and version/conflict rules.

UI: scenario switcher, baseline/current labels, unsaved state, discard confirmation, conflict handling, comparison rows and accessible controls. No fake 'Apply' buttons. If applying a scenario creates a new revision, retain history and explicit provenance.

Test Java service and HTTP endpoints with PostgreSQL Testcontainers: create/edit/list/discard/apply, 404, validation, concurrent/older-revision conflict, persistence. Test frontend and Playwright: baseline -> scenario -> edit -> compare -> discard -> baseline unchanged; and apply -> saved revision reopened. Update documentation, STATUS.md and OpenAPI types.

Java learning: teach service transaction boundaries, version locking/concurrency, DTO validation and 5-10 minute exercise to trigger and understand a 409 conflict. STOP.
```

**Gate:** Proven baseline isolation across refresh/discard/apply, real API persistence and conflict behavior.

## Prompt 09 - Impact Canvas (the only DataCanvas-inspired V1 feature)

```text
Build a focused Impact Canvas that turns *real* workbook metrics and changes into a visual story. Users can pin selected cells/formula outcomes to metric cards, choose one of a few relevant charts (bar/line for compatible numeric data), reorder cards and remove pins. Display verified cell address and source sheet. Keep a visible baseline-vs-scenario comparison; changing a workbook input updates pinned values after recalculation. Never present chart data as live if it is placeholder data.

Use Recharts and Motion responsibly; accessible labels and tables/metric text accompany graphics. If chart data requires a series, let users explicitly select a supported range, validate numeric cells, and gracefully handle mixed/text/empty/error results. Store canvas layout/config and selected metric refs in the Java API with Flyway migration and typed contracts. Restore it after reload.

Write tests for live recalculation, invalid ranges, empty states, pin persistence, scenario switching, performance sanity and screen-reader descriptions. Playwright: pin profit -> change CAC -> see card and chart reflect actual values -> refresh -> pin survives. Update STATUS.md; Java learning: model a one-to-many relationship or JSON configuration persistence, justify choice vs normalized tables and show corresponding Java files. STOP.
```

**Gate:** Real synced charts/cards, no static data substituted, saved layout works.

## Prompt 10 - Formula investigation and UX polish

```text
Complete the formula investigation experience without inventing an AI service. Provide accurate formula text/value/error display, a readable dependency-path explanation, broken-reference and cycle indications where truly detected, and honest 'not fully analyzed' states for dynamic/unsupported expressions. Make selected-cell navigation intuitive across graph, editor, impact inspector and Impact Canvas. Add keyboard and screen reader access, undo/redo coherence, command shortcuts if safe with Univer, loading/empty/error/recovery states, accessible focus and reduced motion.

Audit user flows against docs/DESIGN.md and reference screenshots. Validate responsive behavior with desktop/tablet/mobile; on small screens offer useful investigation panels even if full spreadsheet editing is desktop-prioritized. Fix confusing visual hierarchy, overflow, unnecessary animation and slow interactions. Use discovered design/browser skills when available; record evidence.

Add Vitest/Playwright tests for formula errors and navigation; test keyboard and reduced-motion where feasible; rerun Java tests as relevant. Update STATUS.md. Java learning: explain how backend exception handling and typed API errors map to frontend error states; show actual files and a small exercise. STOP.
```

**Gate:** Clear, usable end-to-end investigation; accessibility and supported formula limits explicitly handled.

## Prompt 11 - Hostile QA, performance, security and integration testing

```text
Act as a skeptical QA engineer, not a feature developer. Audit the implementation against README.md, AGENTS.md, all milestone acceptance gates and docs/DESIGN.md. Inventory every working, partial or unimplemented feature. Create docs/QA-MATRIX.md mapping product requirement -> automated test -> manual check -> outcome. Execute actual frontend unit tests/typecheck/lint/build, backend JUnit/Testcontainers/Flyway tests, Playwright desktop and smaller viewport E2E, and inspect real browser screenshots if browser tools are installed.

Adversarial cases: large/multi-sheet workbooks, quoted sheet names and absolute refs, cycles and formula errors, unsupported INDIRECT/external formulas, rapid edits, no-op edits, race conditions, invalid/oversized JSON, backend unavailable, save conflict, refresh during scenario, discard/apply correctness, malformed selections, undo/redo, repeated mount/unmount, accessibility contrast and keyboard navigation. Verify no errors in browser console and no leaked observers after editor remount. Profile representative workbook performance and log numbers; don't fabricate benchmarks.

Fix critical/high bugs, add regression tests and rerun. Track residual failures honestly and describe any intentionally supported subset. Do not mark milestone complete until critical product flows are demonstrated. Java learning: review actual tests and teach why Testcontainers/JUnit integration catches failures mocks miss. STOP.
```

**Gate:** Evidence-based QA matrix, actual test runs and no known critical failures.

## Prompt 12 - Release-quality demo, documentation and deploy readiness

```text
Prepare Ripple for a public technical demo. Build or polish a real landing page informed by docs/DESIGN.md, featuring an interactive product preview and clear 'Try demo' CTA. Ensure the demo workbook is always available with zero signup, visibly useful seeded formulas and a 30-second guided flow. Add thoughtful product copy explaining 'If I change this cell, what else changes, and why?'; show selected dependency paths, scenario comparisons and the Impact Canvas from the actual app.

Add real local/release setup documentation, project architecture and deployment configuration for static Vite app + Java API + PostgreSQL (choose compatible hosts only after verifying constraints), environment variables, health/readiness checks, database migration strategy, CI checks, safe demo data and a troubleshooting guide. Don't deploy or incur charges without user's explicit request. Avoid SEO or auth feature sprawl.

Produce a 60-90 second demo script; final architecture diagram in docs; 1-page public case study outline; docs/CONTRIBUTION-CANDIDATES.md with specific SDK shortcomings discovered, reproduction, SDK version, proposed focused upstream improvement and tests. Do not submit a Univer PR yet.

Run final verification on a clean checkout or document blockers. Update README and STATUS.md with factual implementation status, commands/outcomes, links to captured screenshots, unresolved limitations and recommended first upstream issue. Summarize Java concepts the owner learned with references to committed code. STOP.
```

**Gate:** Public demo truly works without signup; release plan and contribution candidates reflect the actual project, not aspirations.

---

## Reusable repair prompt (only if a gate fails)

```text
Do NOT start the next Ripple milestone. Read AGENTS.md, STATUS.md and the last prompt's acceptance gate. Reproduce each failing item locally with a deterministic test or browser flow; find the actual root cause; make the minimum fix; run the failed test plus relevant neighboring tests and builds. Update STATUS.md with before/after evidence, exact commands and remaining risks. If this touches Java, teach the Java concept using the files changed and give me a 5-minute exercise. STOP.
```

## Reusable progress audit prompt (use every 2-3 milestones)

```text
Audit Ripple as an independent reviewer. Compare README.md's product promises and AGENTS.md constraints with actual code, running app and test results. Classify each as implemented + verified / implemented + unverified / partial / missing. Inspect git diff, TODOs, feature flags, mocks, hardcoded results and nonfunctional controls. Test one complete product journey in the browser. Create a prioritized, finite correction list, update STATUS.md, and fix only critical regressions needed before the next numbered prompt. Do not implement future milestone features. STOP.
```

## Notes for the human operator
- Run **Prompt 01 first**, even in a blank folder; its job is to discover your installed skills and tools, not to guess them.
- Save/review a Codex checkpoint after each prompt. Don't accept an agent's "complete" claim without test output, app screenshot and a user-visible flow.
- If installed design skills cannot access the live Framer pages, supply your own screenshots to Codex. The exact Reflex services URL was not retrievable during preparation; the official Framer listing describes cinematic visuals and interactive workflow/ROI elements, which can still inform the design.
- Read the Java learning notes after each milestone and try the small exercises before continuing. That will make the Java backend yours rather than a black box.
- Do not start Univer upstream PRs or Tracewise until Ripple's core paths pass Prompt 11.
