# Tasks: Chefsguide v1 — Gap Closure

**Input**: Design documents from `/specs/001-chefsguide-v1/`

**Prerequisites**: plan.md, spec.md, data-model.md, contracts/navigation.md, research.md

**Context**: The prototype (`index.html`) is fully built and passes all constitution checks. These tasks close the four identified quality gaps (G-1 through G-4) within v1 scope. No new features, no Phase 2/3 items.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to

## Gaps Being Closed

| Gap | Summary | User Story |
|-----|---------|------------|
| G-1 | `autoGrow()` not called on restored textareas after navigation | US2 |
| G-3 | No feature detection for `window.print()` | US2 |
| G-4 | `restoreForm` radio button restore logic bug | US2 |

> G-2 (checklists not persisted) is by-design per constitution — no task needed.
> G-5 (accessibility) and G-6 (viewport meta) are Phase 2 — excluded.

---

## Phase 1: Bug Fixes — Form Restore & Export (US2)

**Goal**: Fix form restoration and export reliability issues so that draft autosave, textarea display, and print export work correctly in all cases per spec acceptance scenarios US2-2 through US2-5.

**Independent Test**: Open any form, enter data including radio buttons and long textarea content, navigate away and back — verify all data is restored with correctly sized textareas. Verify export button is hidden if `window.print` is unavailable.

### Implementation

- [ ] T001 [P] [US2] Fix `restoreForm` radio button conditional — change `data[el.name] !== undefined` to proper key-existence check in `index.html` (restoreForm function)
- [ ] T002 [P] [US2] Add `autoGrow()` calls on all restored textareas inside `restoreForm()` after setting values in `index.html` (restoreForm function)
- [ ] T003 [US2] Ensure `go()` calls `restoreForm(formId)` followed by `autoGrow()` on all visible textareas when navigating to a form view in `index.html` (go function)
- [ ] T004 [P] [US2] Add `window.print` feature detection — hide or disable `.btn-export` buttons if `window.print` is not a function in `index.html` (script init section)

**Checkpoint**: All four gaps closed. Manual verification:
1. Navigate to any form with previously saved draft → textareas auto-sized, radio buttons restored correctly
2. Open tool in a browser without `window.print` → export buttons hidden/disabled

---

## Phase 2: Verification & Regression Check

**Goal**: Confirm no regressions introduced by gap fixes and validate against spec acceptance criteria.

- [ ] T005 Manually verify US2 acceptance scenarios 1–5 across Chrome, Edge, Firefox per `index.html`. Also verify FR-022 (FK 7459 contains all official headings) and FR-023 (Rehabiliteringsplan uses two-column task table without signatures).
- [ ] T006 Verify US1 situation view navigation still calls `autoGrow()` correctly (no regression from T003) per `index.html`
- [ ] T007 Verify US8 offline operation — open via file://, confirm zero network requests, confirm all fixes work offline per `index.html`
- [ ] T008 Verify US6 (Avslutssamtal) and US7 (Påminnelse om medverkan) forms match their acceptance scenarios per `index.html`

**Checkpoint**: v1 gap closure complete. All user stories pass acceptance criteria.

---

## Dependencies

```
T001 ─┐
T002 ─┼─→ T005 (verify all fixes together)
T003 ─┘         │
T004 ────────────→ T006, T007 (regression + offline check)
```

- T001, T002, T004 are independent — can be implemented in parallel
- T003 may depend on T002 (autoGrow integration) but can be done independently if autoGrow call is added in both places
- T005–T007 are verification tasks that run after all fixes are applied

## Parallel Execution

**Maximum parallelism**: T001 + T002 + T004 simultaneously (3 tasks, all in different functions within `index.html`)

**Sequential constraint**: T003 should follow T002 (same autoGrow pattern), then T005–T007 after all implementation tasks.

## Implementation Strategy

1. **MVP**: T001 + T004 — fix the data-loss bug (radio buttons) and the export safety check. Immediate value with minimal risk.
2. **Complete**: T002 + T003 — fix textarea sizing on restore. Visual improvement, slightly more invasive change.
3. **Validate**: T005–T007 — manual regression testing across browsers.

## Summary

| Metric | Value |
|--------|-------|
| Total tasks | 7 |
| Implementation tasks | 4 |
| Verification tasks | 3 |
| Parallelizable | 3 (T001, T002, T004) |
| Files modified | 1 (`index.html`) |
| User stories affected | US2 (primary), US1 + US6 (regression check) |
| Estimated scope | Small — all fixes are ≤10 lines each |
