---
name: not-your-qa
description: "Use when implementing, modifying, or verifying features or bugfixes across any project. Enforces autonomous blast radius analysis, task-size calibration, the 5-Dimension Verification Matrix (contracts, edge cases, DB roundtrips with guaranteed cleanup, regressions, and mandatory headless Playwright UI lifecycle), the Verification Gate Function, and deterministic terminal receipts so founders never act as manual QA testers."
metadata:
  category: discipline
  triggers: implement feature, verify feature, test feature, bug fix, quality assurance, e2e testing, not-your-qa, baseworks-testing
---

# not-your-qa: Autonomous Verification & Testing Engine

**Target**: Full-stack web applications, APIs, background workers, and databases across all modern web projects.  
**Core Directive**: **The founder is a creative director and decision maker, NOT a manual QA tester.**  
The agent must autonomously deduce the blast radius of changes, calibrate testing scope, write deterministic verification checks, test across all applicable dimensions (contracts, logic edge cases, database roundtrips with strict cleanup, upstream regressions, and headless Playwright lifecycle rehydration), and provide verifiable terminal receipts before declaring work complete.

---

## ⚡ The Two Iron Laws of Verification

### 1. The Evidence Law (from `verification-before-completion`)
> **"NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE."**  
> Claiming work is complete without executing the verification command in the current turn is dishonesty. If you haven't run the command and read the full output, you cannot claim it passes. "Should work", "looks correct", and "I'm confident" are strictly forbidden.

### 2. The Comprehensive Verification Law (Beyond Single-Surface Testing)
> **"Verification must span the entire execution boundary, not just a single surface."**  
> A feature is NOT verified just because TypeScript compiled. It is NOT verified just because an optimistic UI element moved on screen. It is NOT verified just because a test passed in memory. A feature is verified ONLY when its contract, business logic boundaries, live persistence roundtrip, upstream consumers, and headless browser rehydration are proven by deterministic execution.

---

## 🛠️ Mandatory Prerequisites: Headless Playwright Engine

**Headless Playwright is a non-negotiable prerequisite for all UI and full-stack feature verification (Dimension 5).** Without deterministic E2E verification, the testing engine becomes irrelevant.

* **Strict Prohibition on Silent Bypass**: An agent **MUST NEVER** add a "silent fallback" that bypasses E2E browser verification.
* **Installation Protocol**:
  1. Check if `@playwright/test` or `playwright` is installed in `package.json`.
  2. If Playwright is **NOT** installed, or if the environment or project rules have browser-related restrictions, the agent **MUST ASK THE USER BEFORE INSTALLING WITH A CLEAR REASON**:
     > *"Playwright is a mandatory prerequisite for `@not-your-qa` Dimension 5 (headless E2E lifecycle & F5 rehydration). May I install `@playwright/test` to run the automated headless verification?"*
  3. Once approved, install Playwright and execute all headless tests via CLI (< 5s runtime). Never launch visible interactive desktop browser windows that rot agent context.

---

## 🎯 Task-Size Calibration (Preventing Overkill on Minor Changes)

To maximize engineering velocity while guaranteeing zero defects, the agent autonomously categorizes the task into one of three calibration tiers:

| Tier | Scope & Task Types | Mandatory Verification Dimensions |
| :--- | :--- | :--- |
| **Tier 1: Minor / Cosmetic / Copy** | Typos, copywriting, CSS/Tailwind styling, spacing, color tokens, static layout adjustments. | **Dimension 1 only** (`tsc --noEmit`, linter, file line-count audit). Skip D2–D5. |
| **Tier 2: Logic / Pure Component** | Date math, currency calculators, formatting utilities, Zod validation schemas, isolated presentational subcomponents with no DB mutations. | **Dimension 1 + Dimension 2 + Dimension 4** (Static compiler + boundary/edge unit test + upstream caller regression check). Skip D3/D5. |
| **Tier 3: Full Feature / Data Mutation** | New database tables, schema migrations, server actions, REST/tRPC routes, billing engines, state machines, full UI views. | **All 5 Dimensions (D1, D2, D3, D4, D5)**: Static + Logic + Live DB Roundtrip with Cleanup + Regression Blast Radius + Headless Playwright. |

---

## 🧭 The 4-Phase Autonomous Testing Protocol

When handed any implementation plan, feature task, or bug fix with `@not-your-qa`, the agent executes this sequence autonomously:

```
┌─────────────────────────────────────────────────────────────────┐
│ PHASE 1: AUTONOMOUS BLAST RADIUS & TIER CALIBRATION             │
│ (Inspect codebase, map consumers, select Tier 1, 2, or 3)       │
└────────────────────────────────┬────────────────────────────────┘
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│ PHASE 2: DEFINE FALSIFIABLE INVARIANTS                          │
│ (Convert requirements into 3–5 concrete pass/fail assertions)   │
└────────────────────────────────┬────────────────────────────────┘
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│ PHASE 3: EXECUTE THE 5-DIMENSION VERIFICATION MATRIX            │
│ D1: Static & Contract Gate (Types, Schemas, Linter)             │
│ D2: Logic & Boundary Gate (Nulls, Limits, State Transitions)    │
│ D3: Persistence & Roundtrip Gate (DB/Storage + STRICT CLEANUP)  │
│ D4: Regression & System Gate (Upstream Callers, Crons, APIs)    │
│ D5: UI Lifecycle Gate (MANDATORY Headless Playwright, F5 Reload)│
└────────────────────────────────┬────────────────────────────────┘
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│ PHASE 4: THE GATE FUNCTION & FOUNDER HANDOFF                    │
│ (Run Gate -> Verify output -> Present terminal receipts;         │
│ Founder strictly reviews subjective visual design & aesthetic)  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Autonomous Blast Radius & Regression Audit

The agent **MUST NOT** ask the founder *"What should I test?"* or *"Can you test this?"*  
The agent autonomously runs this 3-step audit:

1. **Upstream Consumer Crawl**:
   - Grep the codebase for all imports of the modified functions, actions, queries, types, or components across `src/`.
   - Identify every screen, API route, or background worker that consumes them.
2. **Data & Storage Dependency Mapping**:
   - Identify every database table, foreign key cascade, or storage bucket touched.
   - Check if changes alter shared operational state (e.g., triage queues, billing records, calendar projections).
3. **Select Applicable Tier**:
   - Classify into Tier 1 (Cosmetic), Tier 2 (Logic), or Tier 3 (Full Feature/Data Mutation).

*(See [`references/blast-radius-analysis.md`](references/blast-radius-analysis.md) for automated dependency-tracing commands).*

---

## Phase 2: Define Falsifiable Invariants

Before writing production code, define 3–5 concrete assertions that can be tested deterministically:

* **Invariant 1 (Execution)**: The operation executes without unhandled exceptions or returning `{ success: false, error: ... }`.
* **Invariant 2 (Persistence Write-Through)**: Querying the database/storage directly confirms that the record actually mutated on disk, not just in memory.
* **Invariant 3 (Edge Case Resilience)**: Submitting empty strings, nulls, boundary values, or duplicate keys triggers handled validation errors (e.g. Zod `400 Bad Request`), never unhandled 500 crashes.
* **Invariant 4 (Regression Invariant)**: All upstream consumers identified in Phase 1 continue to execute cleanly with zero broken contracts.
* **Invariant 5 (Lifecycle & Rehydration)**: For UI views, state survives a hard page refresh (`F5`), and failed server actions trigger graceful optimistic rollback.

---

## Phase 3: The 5-Dimension Verification Matrix

Execute verification systematically across all applicable dimensions:

```
┌─────────────────────────────────────────────────────────────────┐
│               THE 5-DIMENSION VERIFICATION MATRIX               │
├───────────────────┬─────────────────────────────────────────────┤
│ Dimension 1       │ Static & Contract Gate                      │
│ (Contracts)       │ 0 type errors, 0 linter errors, valid schemas│
├───────────────────┼─────────────────────────────────────────────┤
│ Dimension 2       │ Logic, Boundaries & Edge Cases              │
│ (Logic)           │ Happy path, nulls, empty states, limits,    │
│                   │ invalid state machine transitions           │
├───────────────────┼─────────────────────────────────────────────┤
│ Dimension 3       │ Persistence, Transactions & Cleanup         │
│ (Data)            │ Real DB write-through, fresh query check,   │
│                   │ STRICT test record cleanup via try/finally  │
├───────────────────┼─────────────────────────────────────────────┤
│ Dimension 4       │ System Integration & Regression             │
│ (Blast Radius)    │ Upstream callers, background crons/workers, │
│                   │ cross-table projections, no broken screens  │
├───────────────────┼─────────────────────────────────────────────┤
│ Dimension 5       │ UI Lifecycle, Optimistic Rollback & F5      │
│ (User Experience) │ MANDATORY Headless Playwright (<5s),        │
│                   │ optimistic rollback on 500, F5 rehydration  │
└───────────────────┴─────────────────────────────────────────────┘
```

### Dimension 1: Static & Contract Gate
* Run strict project compiler (e.g. `node "./node_modules/typescript/bin/tsc" --noEmit`).
* Run project linter (e.g. `npm run lint`).
* Verify schema contracts (e.g. Zod schemas match API/Server Action payloads).
* Check file size limits (e.g. strict `< 500 lines` rule).
* **Gate**: Exit code `0`, zero errors.

### Dimension 2: Logic, Boundaries & Edge Cases
* Test pure business logic, state machines, and calculations.
* Test boundary inputs: `null`, `undefined`, `""`, whitespace, max length, negative numbers, duplicate IDs.
* Test invalid state transitions (e.g., trying to publish an unapproved post must be rejected).

### Dimension 3: Persistence, Transactions & Roundtrips (With Strict Cleanup)
* **No In-Memory Mocks**: Execute a standalone verification script (e.g. `scripts/verify-feature.mjs`) against the actual database or storage layer.
* **Mutate & Query**: Mutate via the server action or repository, then query the database using a **fresh, un-cached client** to confirm that the row or JSON file actually changed on disk.
* **Database Pollution Guard (MANDATORY)**:
  - All test records **MUST** use a designated test prefix (e.g., `__test_*` or `test-*`).
  - All mutations **MUST** be wrapped in a `try...finally` block that guarantees complete deletion/cleanup of test fixtures, even if an assertion throws an error.
  - Multi-step operations must verify that if step 2 fails, step 1 is rolled back with zero orphaned records.

### Dimension 4: System Integration & Regression Blast Radius
* Run checks on the upstream consumers identified in Phase 1.
* Verify shared projections (e.g., changing a task status updates relevant queue projections and notifications).
* Verify background crons/queues process the new state without getting stuck in infinite loops.

### Dimension 5: UI Lifecycle, Interactive Journeys & Rehydration (Headless Playwright)
* **Playwright Mandatory**: Execute in headless CLI mode (`npx playwright test` or a standalone `scripts/verify-ui.mjs` script via `playwright`). Never open visible desktop windows.
* **Interactive User Journey Validation (No Disconnected Stubs)**: Dimension 5 must exercise the feature's actual critical user path on the rendered interface (e.g. mounting the view, engaging real controls, editing/clearing inputs, triggering state transitions, and verifying that the live DOM reflects the changes without layout breakage, NaN states, or unexpected resets). A generic smoke test or isolated dummy HTML snippet does NOT satisfy Dimension 5.
* **Optimistic Rollback Check**: For mutating actions, verify that a failed server response gracefully rolls back the UI to its prior state with a clear error signal, rather than showing false success.
* **Hard Page Refresh Invariant (`F5`)**: When data mutations are expected to persist, execute `await page.reload()`. Query the rendered DOM to prove that state rehydrated from the server and persisted cleanly.

*(See [`references/verification-dimensions.md`](references/verification-dimensions.md) for exact code patterns for each dimension).*

---

## Phase 4: The Gate Function & Founder Handoff

### The Gate Function (Mandatory Before Any Completion Claim)
Before writing any message stating a feature is done or a bug is fixed:
1. **IDENTIFY**: What command or verification script proves this claim?
2. **RUN**: Execute the full, fresh verification script in the current session.
3. **READ**: Read the complete terminal output, verify exit code `0`, and check for zero errors.
4. **VERIFY**: Does the output definitively prove the falsifiable invariants from Phase 2?
5. **ONLY THEN**: Present the completion claim with **Terminal Receipts**.

### The Founder Handoff Protocol
When presenting work to the founder:
1. **Provide Terminal Receipts**:
   - Static Gate: `tsc --noEmit` exit 0.
   - Persistence Roundtrip: `node scripts/verify-*.mjs` output showing fresh DB read + verified cleanup.
   - UI / Lifecycle Gate: Headless Playwright output showing rehydration across reload.
2. **Provide Blast Radius Confirmation**:
   - List the upstream consumers checked and confirmed working.
3. **Founder's Only Responsibility**:
   - The founder reviews **subjective visual design, spacing, typography, and brand feel**.
   - The founder **NEVER** tests whether buttons save to the database, whether validation works, or whether bugs reproduce. That was already proven by the agent's receipts.

*(See [`references/anti-rationalization.md`](references/anti-rationalization.md) for the complete list of forbidden excuses and rationalizations).*
