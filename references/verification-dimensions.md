# The 5 Verification Dimensions: Technical Implementation Guide

**Goal**: Provide concrete, runnable code patterns and testing methodologies for each of the 5 verification dimensions across full-stack applications.

---

## 🎯 Task-Size Calibration Matrix

Before writing tests, calibrate verification scope based on the change type:

| Tier | Change Type | Mandatory Dimensions |
| :--- | :--- | :--- |
| **Tier 1: Minor / Cosmetic / Copy** | Typos, copywriting, CSS/Tailwind styling, spacing, color tokens. | **Dimension 1 only** (`tsc --noEmit`, linter, line-count). |
| **Tier 2: Logic / Pure Component** | Date math, currency conversions, Zod schemas, isolated UI components with no mutations. | **Dimension 1 + Dimension 2 + Dimension 4**. |
| **Tier 3: Full Feature / Data Mutation** | Schema migrations, server actions, billing cycles, state machines, full views. | **All 5 Dimensions (D1 to D5)**. |

---

## Dimension 1: Static & Contract Gate

### What It Verifies
* Type safety, schema synchronization, linter compliance, file line-count limits (< 500 lines), and import integrity.

### Mandatory Verification Actions
1. **Compiler Check**:
   ```bash
   node "./node_modules/typescript/bin/tsc" --noEmit
   ```
2. **Schema Contract Validation**:
   ```typescript
   import { updateUserLicenseSchema } from '@/lib/validations/billing';
   
   const valid = updateUserLicenseSchema.safeParse({ user_id: '00000000-0000-0000-0000-000000000001', renewal_day: 15, cost: 79 });
   assert(valid.success === true);

   const invalid = updateUserLicenseSchema.safeParse({ user_id: 'invalid-uuid', renewal_day: 45 });
   assert(invalid.success === false);
   ```

---

## Dimension 2: Logic, Boundaries & Edge Cases

### What It Verifies
* Pure business logic, date calculations across month/year boundaries, state machine transition rules, boundary conditions, and error states.

### Mandatory Test Matrix
* **The Happy Path**: Standard valid input produces the expected output.
* **Null / Undefined / Empty Input**: Empty strings `""`, whitespace `"   "`, `null`, `undefined`, empty arrays `[]`.
* **Boundary Limits**: Max characters, negative numbers, day > 31, zero amounts.
* **Year & Month Boundary Rollovers**: E.g. advancing December 15 by 1 month rolls over to January 15 of next year.
* **Invalid State Transitions**: Attempting to transition from `draft` directly to `published` without review.

---

## Dimension 3: Persistence, Transactions & Strict Cleanup

### What It Verifies
* Changes are written through to the real persistence layer (PostgreSQL, Supabase Storage, Redis, SQLite), NOT just cached in memory or optimistic React state.

### 🛡️ Database Pollution Guard (MANDATORY Cleanup Invariant)
* Every test mutation **MUST** use a designated test prefix (`__test_*` or `test-*`).
* Every test mutation **MUST** be wrapped in a `try...finally` block to ensure cleanup runs even if an assertion throws an error.

### Standalone Script Pattern (`scripts/verify-persistence.mjs`)
```javascript
import { db } from '../src/lib/db.ts';
import { toggleTaskStatus } from '../src/lib/tasks/service.ts';
import assert from 'node:assert';

async function testPersistenceRoundtrip() {
  const testTaskId = `__test_${Date.now()}`;

  try {
    // Step 1: Execute mutation through application code
    const result = await toggleTaskStatus(db, testTaskId, true);
    assert.strictEqual(result.success, true, 'Mutation must succeed');

    // Step 2: Query directly from a fresh un-cached connection
    const record = await db
      .selectFrom('tasks')
      .selectAll()
      .where('id', '=', testTaskId)
      .executeTakeFirst();

    assert(record, 'Record must exist on disk');
    assert.strictEqual(record.is_completed, true, 'Database record must be persisted as completed');
    console.log('✅ Dimension 3 Passed: Live persistence roundtrip verified.');
  } finally {
    // Step 3: GUARANTEED CLEANUP — Purge test fixtures even on test failure
    await db.deleteFrom('tasks').where('id', '=', testTaskId).execute();
    console.log('🧹 Cleanup verified: test record removed from database.');
  }
}

testPersistenceRoundtrip().catch(err => {
  console.error('❌ Dimension 3 Failed:', err);
  process.exit(1);
});
```

---

## Dimension 4: System Integration & Regression Blast Radius

### What It Verifies
* Upstream consumers, shared projections, background workers, and cross-table cascades remain intact.

### What to Verify
1. **Attention & Queue Projections**:
   * E.g., when an item expires, does the triage query synthesize an immediate renewal task? When it expires next month, does it stay quiet?
2. **Background Crons / Scheduled Cycles**:
   * Does the renewal cycle pick up eligible items and skip already-processed ones?
3. **Cross-Entity Cascades**:
   * E.g., archiving an organization archives its pending invites and revokes active API tokens.

---

## Dimension 5: UI Lifecycle, Optimistic Rollback & Rehydration

### What It Verifies
* The user interface renders correctly, gestures trigger actions, failures trigger optimistic rollbacks, and state survives route navigation and hard page reloads (**F5**).

### ⚡ Prerequisite Policy: Playwright Is Mandatory
* **No Silent Bypass**: Never add a fallback that skips E2E browser testing.
* **If Not Installed**: If Playwright is missing or restricted, **ask the user before installing with a clear reason**:
  > *"Playwright is a mandatory prerequisite for `@not-your-qa` Dimension 5 (headless E2E lifecycle & F5 rehydration). May I install `@playwright/test` to run automated verification?"*
* **Headless Only (< 5s)**: Execute all tests in headless mode (`npx playwright test` or standalone script). Never open visible interactive desktop windows.
* **Hard Page Refresh Invariant (`F5`)**: Always run `await page.reload()` and assert DOM rehydration from disk.
* **Optimistic Rollback Check**: Mock a 500 network error and assert UI reverts gracefully.
