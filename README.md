# 🛡️ not-your-qa

> **"The founder is a creative director and decision maker, NOT a manual QA tester."**

Stop your AI coding agents (Claude Code, Cursor, Windsurf, Antigravity) from hallucinating completion. Enforce blast-radius analysis, task-size calibration, live DB roundtrips with guaranteed cleanup, headless Playwright rehydration, and deterministic terminal receipts before declaring any task complete.

Built and battle-tested by [BaseWorks](https://baseworks.in).

---

## ⚡ The Lazy Founder Prompt (1-Click Install)

Don't want to read this whole document? **Copy and paste this into Claude Code, Cursor, Windsurf, or Antigravity right now:**

```markdown
Read the autonomous verification engine from https://github.com/OL-Arvind/not-your-qa.
Adopt the 5-Dimension Verification Matrix and the Rule of Terminal Receipts for this entire repository.
Never claim any feature or bugfix is complete without executing the gate command and presenting fresh terminal receipts (exit code 0).
I am a decision maker, NOT your manual QA tester. Calibrate scope (Tier 1/2/3) and verify contracts, logic boundaries, live DB roundtrips with cleanup, upstream regressions, and headless Playwright UI rehydration.
```

---

## The Problem: Agent Delusion

When AI coding agents complete a task, they naturally tend toward optimistic assumptions:
* ❌ *"TypeScript compiled with 0 errors, so it works."* (Syntax != runtime logic).
* ❌ *"The optimistic UI moved to Completed, so it works."* (React `useState` updates even on unhandled 500s).
* ❌ *"I visually inspected the file and it looks correct."* (Looking at code is not executing code).
* ❌ *"I'll let the user test it in their browser."* (Wasting founder time on basic QA).

`not-your-qa` eliminates these loopholes with **The Evidence Law**:  
> **NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE.**

---

## The 5-Dimension Verification Matrix

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
│ (Data)            │ Real DB/Storage write-through, ACID rollback│
│                   │ on error, STRICT cleanup via try/finally    │
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

---

## 🎯 Task-Size Calibration (Preventing Overkill)

Not every change requires full E2E testing. The agent autonomously calibrates:

| Tier | Change Type | Mandatory Dimensions |
| :--- | :--- | :--- |
| **Tier 1: Minor / Cosmetic** | Typos, copywriting, CSS styling, spacing tokens. | **Dimension 1 only** (`tsc --noEmit`, linter). |
| **Tier 2: Logic / Pure Unit** | Date math, currency conversions, Zod schemas, pure UI components. | **Dimension 1 + 2 + 4** (Types + unit tests + upstream callers). |
| **Tier 3: Feature / Mutation** | DB migrations, server actions, billing cycles, full views. | **All 5 Dimensions (D1–D5)**. |

---

## 🚀 Installation & Multi-Platform Compatibility

### 1. Claude Code
Add to your project's skills directory:
```bash
git clone https://github.com/OL-Arvind/not-your-qa.git .claude/skills/not-your-qa
```
Or install globally across all projects:
```bash
git clone https://github.com/OL-Arvind/not-your-qa.git ~/.claude/skills/not-your-qa
```

### 2. Google Antigravity / Gemini IDE
Add to your workspace customizations:
```bash
git clone https://github.com/OL-Arvind/not-your-qa.git .agents/skills/not-your-qa
```

### 3. Cursor & Windsurf
Add this rule directly to your `.cursorrules` or `.windsurfrules`:
```markdown
Enforce verification rules and the 5-Dimension Matrix from: https://raw.githubusercontent.com/OL-Arvind/not-your-qa/main/SKILL.md
```

---

## 🧾 Terminal Receipts: What Success Looks Like

When your agent claims work is done, it must present **Terminal Receipts**:

```bash
$ node ./node_modules/typescript/bin/tsc --noEmit
Exit 0: No type errors.

$ node scripts/verify-persistence.mjs
✅ Dimension 3 Passed: Live persistence roundtrip verified.
🧹 Cleanup verified: test record removed from database.

$ npx playwright test tests/e2e/task-lifecycle.spec.ts
Running 1 test using 1 worker
  ✓ task survives F5 hard reload (1.8s)
1 passed (2.1s)
```

If the agent cannot provide terminal receipts, **the task is not complete.**

---

## 📚 Deep Dive Reference Guides

* [Anti-Rationalization & Psychology of Agent Failure](references/anti-rationalization.md)
* [Autonomous Blast Radius & Regression Analysis](references/blast-radius-analysis.md)
* [Headless Playwright Patterns (< 5s E2E Execution)](references/headless-playwright-patterns.md)
* [The 5 Verification Dimensions Technical Guide](references/verification-dimensions.md)
* [The Verification Matrix Architecture](references/verification-pyramid.md)

---

## 📜 License
MIT License © 2026 [Aravind Bhati](https://github.com/OL-Arvind) / [BaseWorks](https://baseworks.in).
