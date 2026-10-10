# Headless Playwright Patterns: Rehydration & Optimistic Rollbacks

**Goal**: Provide fast, lightweight, headless browser verification patterns that run in < 5 seconds without consuming agent context tokens or requiring human manual clicking.

---

## ⚡ Prerequisite Policy: Playwright Is Mandatory

* **Non-Negotiable Requirement**: Headless browser verification (Dimension 5) is mandatory for all full-stack UI features. Without it, the testing engine is incomplete.
* **No Silent Bypass**: Agents **MUST NEVER** add a silent fallback that skips Playwright.
* **If Not Installed**: If Playwright (`@playwright/test` or `playwright`) is not present in `package.json`, or if project rules restrict browser tools, **ask the user before installing with a clear reason**:
  > *"Playwright is a mandatory prerequisite for `@not-your-qa` Dimension 5 (headless E2E lifecycle & F5 rehydration). May I install `@playwright/test` to run the automated headless verification?"*
* **Headless Only**: Once approved/installed, always run in headless mode (`headless: true`). Never open visible desktop browser windows.

---

## 1. Why Headless Playwright Beats Interactive AI Clicking

| Ad-Hoc Interactive AI Browser Clicking (Bad) | Headless Playwright Script (Good) |
| :--- | :--- |
| Takes 60–90 seconds per click. | Runs entire test suite in **3–5 seconds**. |
| Takes full DOM dumps and screenshots into LLM context. | Outputs clean 3-line terminal receipts; zero context rot. |
| Agent improvises what to test; misses reload & edge cases. | Enforces strict, deterministic assertions every single time. |
| High token cost; agent forgets requirements. | Zero LLM token cost during execution. |

---

## 2. Testing The Positive Lifecycle & F5 Rehydration

Every UI mutation test must follow this sequence:

```typescript
import { test, expect } from '@playwright/test';

test.describe('Task Persistence Suite', () => {
  test('marking task complete persists to DB and survives F5 refresh', async ({ page }) => {
    // 1. Navigate to target screen
    await page.goto('http://localhost:3000/dashboard');
    await page.waitForLoadState('networkidle');

    // 2. Identify target item
    const taskRow = page.locator('text=Deploy authentication webhook').first();
    await expect(taskRow).toBeVisible();

    // 3. Perform the mutation action
    const checkbox = taskRow.locator('button[type="button"]');
    await checkbox.click();

    // 4. Assert instant feedback (Optimistic UI & No Error Toast)
    const errorToast = page.locator('.toast:has-text("Error")');
    await expect(errorToast).toHaveCount(0);

    // 5. CRITICAL: Hard Page Refresh (F5) Invariant
    // Never trust in-memory state alone. Reload the browser to test server rehydration.
    await page.reload();
    await page.waitForLoadState('networkidle');

    // 6. Assert persistence across reload
    const completedHeader = page.locator('text=Completed');
    await expect(completedHeader).toBeVisible();
    
    // Ensure item is NOT in the active To-Do list
    const activeTasksList = page.locator('[data-section="active-tasks"]');
    await expect(activeTasksList).not.toContainText('Deploy authentication webhook');
  });
});
```

---

## 3. Testing Optimistic Rollback on Server Failure

A robust system does not just test success; it tests what happens when the backend fails:

```typescript
test('optimistic UI rolls back gracefully if server returns 500 error', async ({ page }) => {
  await page.goto('http://localhost:3000/dashboard');
  await page.waitForLoadState('networkidle');

  // Intercept the server action or mutation endpoint and force a 500 error
  await page.route('**/api/**', route => route.abort('failed'));

  const checkbox = page.locator('button[role="checkbox"]').first();
  await checkbox.click();

  // Assert error notification appears
  await expect(page.locator('.toast')).toContainText('Failed');

  // Assert item rolls back to unchecked / uncompleted state
  await expect(checkbox).toHaveAttribute('aria-checked', 'false');
});
```

---

## 4. Fast Standalone Script Pattern (Zero-Config)

If a project doesn't have a configured `@playwright/test` runner yet, write a rapid standalone verification script in `scripts/verify-ui.mjs` using the raw `playwright` package:

```javascript
import { chromium } from 'playwright';

async function runSmokeTest() {
  console.log('🚀 Launching headless browser...');
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();

  try {
    console.log('1. Navigating to Dashboard...');
    await page.goto('http://localhost:3000/dashboard');
    await page.waitForLoadState('networkidle');

    console.log('2. Triggering task completion...');
    const checkbox = page.locator('button[role="checkbox"]').first();
    await checkbox.click();

    console.log('3. Waiting for server action...');
    await page.waitForTimeout(1000);

    console.log('4. Testing F5 reload persistence...');
    await page.reload();
    await page.waitForLoadState('networkidle');

    // Assert that no error toast is present
    const hasError = await page.locator('.toast:has-text("Error")').isVisible();
    if (hasError) {
      throw new Error('❌ Test failed: Error toast found on reload!');
    }

    console.log('✅ UI verification passed: State persisted across F5 reload!');
  } finally {
    await browser.close();
  }
}

runSmokeTest().catch((err) => {
  console.error(err);
  process.exit(1);
});
```

---

## 5. Execution Commands

Run your verification script directly in the terminal:

```bash
# Full test runner:
npx playwright test

# Or standalone script:
node scripts/verify-ui.mjs
```

* Exit Code `0`: Safe to ship.
* Exit Code `1`: Broken. Must be fixed before handoff.
