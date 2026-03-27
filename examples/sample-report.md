# QA Exploration Report — TaskFlow

> Generated: 2026-03-27 14:32 UTC
> Target: http://localhost:3000
> Framework: Next.js (App Router)
> Rounds completed: 3/3

## Summary

| Metric | Value |
|--------|-------|
| Health Score | **62 / 100** |
| Pages discovered | 12 |
| Pages tested | 12 |
| Total bugs found | 4 |
| P0 (Critical) | 1 |
| P1 (Major) | 2 |
| P2 (Minor) | 1 |
| UX suggestions | 1 |

---

## Bugs

### P0-001: Form submission crashes app with unhandled TypeError

| Field | Detail |
|-------|--------|
| Severity | **P0 — Critical** |
| Page | `/tasks/new` |
| Round | 2 (Boundary) |
| Screenshot | `screenshots/p0-001-form-crash.png` |

**Steps to reproduce:**
1. Navigate to `http://localhost:3000/tasks/new`
2. Fill in "Task name" with a valid value: `Buy groceries`
3. Leave the "Due date" field empty
4. Click "Create Task"

**Expected:** Validation error shown, task not created.

**Actual:** App crashes with white screen. Console shows:
```
Uncaught TypeError: Cannot read properties of null (reading 'toISOString')
    at formatDueDate (TaskForm.tsx:47)
    at handleSubmit (TaskForm.tsx:82)
```

**Impact:** Users cannot create tasks without a due date. The entire app becomes unresponsive until page refresh.

---

### P1-001: DELETE /api/tasks/:id returns 404 for valid task IDs

| Field | Detail |
|-------|--------|
| Severity | **P1 — Major** |
| Page | `/tasks` (task list) |
| Round | 1 (Happy Path) |
| Screenshot | `screenshots/p1-001-delete-404.png` |

**Steps to reproduce:**
1. Navigate to `http://localhost:3000/tasks`
2. Confirm at least one task exists in the list (e.g., task ID `a3f8c2`)
3. Click the trash icon on any task row
4. Confirm deletion in the modal

**Expected:** Task is deleted, list refreshes without the deleted item.

**Actual:** Toast notification shows "Error: Task not found". Network tab shows `DELETE /api/tasks/a3f8c2` returning `404`. Task remains in the list.

**Notes:** Likely a route parameter mismatch — the frontend sends the short ID but the API expects the full UUID.

---

### P1-002: No loading state during task list fetch

| Field | Detail |
|-------|--------|
| Severity | **P1 — Major** |
| Page | `/tasks` |
| Round | 1 (Happy Path) |
| Screenshot | `screenshots/p1-002-no-loading.png` |

**Steps to reproduce:**
1. Throttle network to "Slow 3G" in browser DevTools
2. Navigate to `http://localhost:3000/tasks`
3. Observe the page during data fetch

**Expected:** Loading spinner or skeleton UI while tasks load.

**Actual:** Page shows an empty white area for 3-8 seconds before tasks appear. No loading indicator, no skeleton, no feedback. Users may think the app is broken or that they have no tasks.

---

### P2-001: "Create Task" button misaligned on mobile viewport

| Field | Detail |
|-------|--------|
| Severity | **P2 — Minor** |
| Page | `/tasks/new` |
| Round | 1 (Happy Path) |
| Screenshot | `screenshots/p2-001-button-misalign.png` |

**Steps to reproduce:**
1. Set viewport to 375x667 (iPhone SE)
2. Navigate to `http://localhost:3000/tasks/new`
3. Scroll to the bottom of the form

**Expected:** "Create Task" button is full-width and properly aligned within the form container.

**Actual:** Button overflows the form container by ~20px on the right side. The right edge is clipped by the viewport. Still clickable, but visually broken.

---

## UX Suggestions

### UX-001: Add keyboard shortcut for task creation

| Field | Detail |
|-------|--------|
| Page | Global |
| Impact | Low effort, high frequency |

**Observation:** Power users navigate between `/tasks` and `/tasks/new` frequently. There is no keyboard shortcut to create a new task.

**Suggestion:** Add `Ctrl+N` / `Cmd+N` shortcut to open the new task form from any page. Display the shortcut hint in the "Create Task" button tooltip.

---

## UX Journey Scores

### Journey 1: Create a new task

| Dimension | Score | Notes |
|-----------|-------|-------|
| Discoverability | 8/10 | Clear "+" button in header |
| Efficiency | 5/10 | No keyboard shortcut, requires 4 clicks minimum |
| Error handling | 2/10 | Crashes on empty due date (P0-001) |
| Feedback | 6/10 | Success toast works, but no inline validation |
| **Overall** | **5.3/10** | |

### Journey 2: View and manage task list

| Dimension | Score | Notes |
|-----------|-------|-------|
| Discoverability | 9/10 | Default landing page, intuitive layout |
| Efficiency | 7/10 | Bulk actions available, filters work |
| Error handling | 4/10 | Delete returns 404 (P1-001) |
| Feedback | 3/10 | No loading state (P1-002), error toast is vague |
| **Overall** | **5.8/10** | |

---

## Test Coverage by Round

| Round | Pages | Bugs Found | Duration |
|-------|-------|------------|----------|
| Round 1: Happy Path | 12/12 | 2 (P1-001, P1-002) | 4m 12s |
| Round 2: Boundary | 8/12 | 1 (P0-001) | 6m 47s |
| Round 3: Destructive | 6/12 | 1 (P2-001) | 3m 31s |

**Total duration:** 14m 30s
