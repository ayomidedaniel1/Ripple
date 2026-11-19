# Ripple

**See the impact of every change.**

Ripple is an interactive spreadsheet debugger and scenario explorer built with the open-source **Univer Office SDK**. It helps people answer: **“If I change this cell, what else changes, and why?”**

> Status: **project specification / not implemented**. This starter pack contains instructions and a milestone-by-milestone build plan. Do not mistake documented features for working software.

## The problem
Complex workbooks show calculated numbers without making the *chain of causality* easy to inspect. When a business assumption changes, users struggle to see which other cells changed, whether a formula is broken, and what would happen if they try another assumption. Traditional formula-auditing tools are useful, but full-workbook impact analysis and clear scenario comparisons are awkward.

## Product promise
1. **Edit a real workbook:** Univer provides spreadsheet editing, formulas, calculations, and workbook snapshots.
2. **Explore relationships:** select a cell, see precedents/dependents in an interactive graph, and navigate between nodes and sheet cells.
3. **Understand impact:** capture a baseline, make a change, and see accurate before/after results, paths, and changes based on recalculated values.
4. **Experiment safely:** create a scenario, edit assumptions in isolation, compare to baseline, apply or discard.
5. **Tell a visual story:** pin outcome metrics and small linked visualizations to an **Impact Canvas**. It is an impact-analysis workspace, not a general dashboard builder.

### Flagship demonstration
Open a seeded multi-sheet business model. Change customer acquisition cost. Ripple highlights downstream formulas, shows profit and runway changes, and updates a pinned comparison chart. Discard the scenario to restore the unmodified baseline. All displayed numbers come from the actual Univer workbook.

## Non-goals for MVP
No AI chat, universal Excel compatibility, native charts, commercial SDK extensions, multiplayer editing, file import/export guarantees, arbitrary visual dashboard builder, autonomous recommendations, mobile spreadsheet editing, or full support for every Excel formula. Features beyond the verified SDK subset must be marked unsupported.

## Proposed technical stack
| Layer | Tooling | Responsibility |
| --- | --- | --- |
| Frontend | Vite, React, strict TypeScript, TanStack Router + Query | Application UI, data fetching, navigation |
| Spreadsheet | Univer Sheets Core preset | Workbook editing, calculation, snapshots |
| Graph | `@xyflow/react` | Dependency navigation and change paths |
| Visualizations | Recharts, Motion | Small impact charts, metric transitions |
| Design | Tailwind CSS, accessible primitives | Responsive UI and interaction patterns |
| API | Java (prefer Java 25 LTS if available), Spring Boot, Gradle | Projects, snapshots, scenarios, revisions, validation |
| Data | PostgreSQL, Flyway, Spring Data JPA | Persistent project and workbook versions |
| Integration | OpenAPI-generated TS types when practical | Typed frontend/backend contract |
| Quality | Vitest, Testing Library, Playwright, JUnit 5, Testcontainers | Functional and regression verification |

Java runs the API; Univer runs inside the web application. Java does **not** reimplement or calculate the workbook formulas.

## Intended repository structure
```text
ripple/
├── apps/
│   ├── web/                    # Vite + React
│   └── api/                    # Spring Boot + Gradle
├── docs/                       # Architecture/design/testing notes produced by implementation
├── AGENTS.md
├── PROMPTS.md                 # Codex runbook: one prompt at a time
├── STATUS.md                  # Actual progress, tests, blockers
├── README.md
├── pnpm-workspace.yaml
└── compose.yaml                # PostgreSQL for local development
```
The structure may evolve with evidence. Do not create empty directories just to match a diagram.

## Primary workflows and acceptance criteria
**Explore:** load a real workbook, select formula/input cells, view verified upstream/downstream dependencies and navigate to their sources. Cross-sheet links and absolute/relative references must be explicitly tested. Mark unsupported or partial coverage rather than asserting completeness.

**Compare:** start a baseline, edit an input, wait for calculations to settle, then show true before/after values. Rapid edits must never display stale comparisons. Support undo/redo.

**Experiment:** create isolated scenario from a baseline snapshot; modifying it does not change baseline. Discard leaves baseline untouched; apply produces a clearly versioned, persisted change.

**Visualize:** pin real workbook values to Impact Canvas, select a simple supported chart, and update it in response to workbook recalculations. Saved pins and restored values survive reload.

**Persist:** save/reopen projects and versions through the Java API with clear conflicts, errors, and validation. Seed demo data works without paid services or authentication.

## Design references
- Knotch: https://knotch.framer.ai/
- Reflex services: https://reflex-template.framer.ai/services

These are **directional inspiration**, not templates to clone. Aim for premium editorial rhythm, visually focused sections, restrained motion, meaningful interactive demos, crisp surfaces, and a serious technical workspace. See `PROMPTS.md` Prompt 02 for discovery and implementation requirements. Inspect any installed local UI/browser/design skills before starting UI work; record what is actually available.

## Implementation sequence
Follow `PROMPTS.md`, one numbered prompt at a time. The prompts cover discovery, product design, project setup, Java persistence, Univer integration, dependency engine, change tracking, scenarios, Impact Canvas, QA, and release. **Do not skip gate verification.**

## Local development
**Not yet runnable**. Prompt 03 will scaffold the actual project, pin framework versions, and add verified commands here. Intended prerequisites: Git, Node.js LTS, pnpm, a supported Java LTS and Gradle wrapper, Docker Desktop / Compose. Commands are intentionally not invented before the project exists.

## Engineering principles
- Verify installed Univer APIs before using them. SDK behavior is authoritative, not README promises.
- Prefer integration tests proving actual spreadsheet calculations and persistence over UI mocks.
- Use precise change explanations grounded in cell references and recorded values.
- Treat scenario snapshots as immutable baselines; isolate all experimental edits.
- Accessible controls, reduced motion, helpful empty/loading/error states and realistic seeded data are required.
- Document limitations around dynamic arrays, `INDIRECT`, volatile formulas, external links, large ranges and cross-sheet references.
- Every milestone updates `STATUS.md` and teaches one relevant Java concept using the real code.

## Sources and upstream
- Univer: https://github.com/dream-num/univer
- Univer React integration: https://docs.univer.ai/guides/sheets/getting-started/integrations/react
- Univer installation: https://docs.univer.ai/guides/sheets/getting-started/installation
- TanStack Router Vite integration: https://tanstack.com/router/latest/docs/installation/with-vite
- Univer contribution guide: https://github.com/dream-num/univer/blob/dev/CONTRIBUTING.md

## Open-source contribution plan
After Ripple MVP is stable, document concrete Univer shortcomings with minimal reproducible examples, tests, and version information. Check upstream issues and discuss improvements before opening focused PRs. Contributing upstream is a **later phase**, not a Ripple milestone.
