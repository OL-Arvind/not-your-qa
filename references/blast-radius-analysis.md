# Blast Radius & Regression Analysis Guide

**Goal**: Enable any AI coding agent to independently determine what existing features might break before modifying or shipping code, without needing the founder to explain the system context.

---

## 1. The Autonomous 3-Step Blast Radius Audit

Whenever an agent touches an existing codebase, run this sequence before touching code:

### Step 1: Upstream Dependency Crawl
Identify every file that imports the entity you plan to modify:

```powershell
# PowerShell: Find all references to a function, type, or file in src/
Get-ChildItem -Path src -Recurse -Include "*.ts","*.tsx" | Select-String "functionOrTypeName"
```

```bash
# Bash / Ripgrep:
rg -g "*.{ts,tsx}" "functionOrTypeName" src/
```

*Questions the agent must answer*:
* Is this function, store, or type shared across multiple screens or routes?
* If I change this return signature or parameter list, which parent components will break?
* Are there unit tests or test scripts that already import this?

### Step 2: Shared State & Operational Projection Audit
Check if the domain entity is read by other background crons, queries, or projections:

* **Triage / Queue Systems**: Does altering this state remove it from the triage queue or move it between buckets?
* **Calendar & Temporal Projections**: Does changing this date mutate projections on scheduled views?
* **Client Portal / External Views**: Does this state change affect what an external client sees on mobile or sub-dashboards?
* **Pipeline Statuses**: Does completing an action cascade into downstream lifecycle stages (e.g. `lead` -> `active` -> `archived`)?

### Step 3: Git Status & Pre-Commit Diff Hygiene
Before committing changes, review the exact diff to ensure no unrelated files or unintended side effects were introduced:

```bash
git status --short
git diff --stat
```

---

## 2. Regression Risk Classification Matrix

| Change Type | Blast Radius | Mandatory Verification Check |
| :--- | :--- | :--- |
| **Database Schema / Migration** | **High** | Check all queries referencing the table, check RLS policies, check rollback scripts. |
| **Server Action / API Mutation** | **High** | Check all UI callers, verify error handling, test roundtrip DB write, test invalid payloads. |
| **Domain Type Definition (`types/`)** | **High** | Run full project compiler (`tsc --noEmit`) to detect broken consumers across all routes. |
| **Shared Layout / Navigation Shell** | **Medium** | Verify responsiveness across mobile, tablet, desktop viewports and ensure sub-routes mount. |
| **Isolated UI Subcomponent** | **Low** | Verify props, container layout, and component-level re-render behavior. |

---

## 3. The "No-Assumptions" Rule

If an agent is unsure whether a change affects another module:
1. Grep the codebase for all occurrences of the model name or action.
2. Read the caller files directly.
3. Check the project documentation (`docs/domain-model.md`, `docs/state-machines.md`).
4. **NEVER** guess or silently assume another module will handle the change.
