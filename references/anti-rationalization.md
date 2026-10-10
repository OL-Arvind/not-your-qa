# Anti-Rationalization & Verification Discipline

**Goal**: Close every mental loophole AI coding agents use to skip verification, claim false success, or push untested code to founders.

---

## 1. The Psychology of Agent Failure

When an AI coding agent is tasked with building or fixing code, it naturally tends toward optimistic assumptions. Under context limits or time pressure, agents generate predictable rationalizations to avoid real verification:

* *"This change is only 2 lines, it can't possibly break anything."*
* *"TypeScript compiled cleanly, so the logic is correct."*
* *"I checked the function code and visually verified it."*
* *"The optimistic UI moved to Completed, so it works."*
* *"I'll let the user test it in their browser and tell me if it works."*

**Every single one of these excuses is an engineering failure.**

---

## 2. The Two Iron Laws

### Law 1: The Evidence Law (from `verification-before-completion`)
```
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```
* If you haven't run the verification command in the current message turn, you cannot claim it passes.
* Confidence is NOT evidence. A clean linter is NOT a running test.

### Law 2: The Bug Fix Reproduction Law (from `test-driven-development`)
```
NO BUG FIX WITHOUT AN EXECUTABLE REPRODUCTION FIRST (RED -> GREEN)
```
* If fixing a bug: write a minimal reproduction script or test that FAILS against the current code.
* Run it. Observe the failure (RED).
* Implement the fix.
* Run it again. Observe it pass (GREEN).
* If you didn't watch the test fail first, you don't know if you fixed the real bug or just masked the symptom.

---

## 3. The Red Flags List (STOP Immediately)

If an agent catches itself generating any of these phrases or thoughts, it must **STOP** immediately and execute the verification matrix:

1. ❌ **"I inspected the file and verified the logic."**  
   *Reality*: Looking at code is not executing code. Syntax analysis misses runtime errors, missing parameters, and database constraints.
2. ❌ **"The build succeeded / TypeScript has 0 errors."**  
   *Reality*: TypeScript verifies type signatures, not database connections, SQL queries, or network failures.
3. ❌ **"The optimistic UI updated on the screen, so it works."**  
   *Reality*: React `useState` updates in memory instantly even if the server threw an unhandled 500 error.
4. ❌ **"I tested F5 reload, so everything is working."**  
   *Reality*: F5 reload only tests browser rehydration. It does not test backend APIs, background crons, database transaction rollbacks, or edge cases.
5. ❌ **"I'll prompt the user to test it."**  
   *Reality*: The founder's time is extremely scarce. Handing off untested code wastes the founder's time on trivial QA.

---

## 4. The Rationalization Counter-Table

| Agent Excuse | Objective Reality | Non-Negotiable Action |
| :--- | :--- | :--- |
| *"I don't need to write a script, I already saw it work."* | In-memory execution proves nothing about persistence. | Run a standalone script querying a fresh database connection. |
| *"I don't know what existing features might break."* | The agent has full access to `grep`, `git`, and the file tree. | Execute Phase 1: Upstream Dependency Crawl before touching code. |
| *"Running tests takes too long."* | Running a headless script takes **3 seconds**. Manual debugging with a founder takes **30 minutes**. | Execute the automated script and report terminal outputs. |
| *"I tested it in my head."* | Mental simulations routinely miss race conditions and edge cases. | Run the real script against the real environment. |
| *"If 3 fixes failed, maybe 1 more will work."* | 3 failed fixes indicates an architectural flaw, not a typo. | STOP. Question the architecture and discuss with the founder. |

---

## 5. The Rule of Terminal Receipts

**No task is marked complete without Terminal Receipts.**

When an agent states *"Feature X is working"*, it must provide:
1. The exact command that was executed (e.g. `node ./node_modules/typescript/bin/tsc --noEmit`, `node scripts/verify-task.mjs`).
2. The exit code (`0`).
3. The stdout proving the test assertion passed across all applicable dimensions.

If an agent cannot provide a terminal receipt, the work is **not done**.
