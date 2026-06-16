# UI Contract: Navigation API

**Feature**: 001-chefsguide-v1 | **Date**: 2026-05-21

## Public Functions

These are the only navigation functions exposed globally. All view transitions must go through these.

### `go(id: string): void`

Navigate forward to the specified view.

**Preconditions**:
- `id` must match a `<div class="view" id="{id}">` in the DOM
- View must exist (no validation performed — caller responsibility)

**Postconditions**:
- `navStack` has `id` appended
- Previous view loses `.active` class
- Target view gains `.active` class
- Back button becomes visible
- `window.scrollTo(0, 0)` is called
- All visible textareas in target view have `autoGrow()` called
- If target is a form view, `restoreForm(formId)` is called

**Side effects**: None beyond DOM manipulation

---

### `back(): void`

Navigate to the previous view in history.

**Preconditions**:
- `navStack.length > 1` (no-op if at home)

**Postconditions**:
- Last entry removed from `navStack`
- New last entry becomes active view
- If `navStack.length === 1`, back button is hidden
- `window.scrollTo(0, 0)` is called
- `autoGrow()` called on visible textareas

---

### `home(): void`

Reset navigation to home view.

**Preconditions**: None

**Postconditions**:
- `navStack` reset to `['view-home']`
- Home view is active
- Back button is hidden
- `window.scrollTo(0, 0)` is called

---

## Form API

### `saveForm(formId: string): void`

Serialize all named inputs in the form to localStorage.

**Storage format**: `localStorage["tre_sjuk_v3_{formId}"] = JSON.stringify({ name: value, ... })`

**Input types**:
- `text` / `textarea`: stored as string value
- `checkbox`: stored as boolean
- `radio`: stored as string value of checked radio's `value` attribute

---

### `restoreForm(formId: string): void`

Restore form fields from localStorage.

**Preconditions**: `HAS_STORAGE === true`

**Behaviour**: For each input with a `name` attribute, if a matching key exists in stored data, set the value. Call `autoGrow()` on restored textareas.

---

### `clearForm(formId: string, announce: boolean): void`

Clear all form fields and remove localStorage entry.

**Postconditions**:
- All inputs reset (empty string, unchecked)
- `localStorage["tre_sjuk_v3_{formId}"]` removed
- If `announce === true`, toast shown: "Formuläret rensat."

---

### `exportForm(formId: string): void`

Save current form state, open print dialog, and clear on completion.

**Sequence**:
1. `saveForm(formId)` — ensure latest state persisted
2. Set `pendingClear = formId`
3. `window.print()` — opens browser print dialog
4. On `afterprint` event: `clearForm(formId, false)` → toast "Exporterat — uppgifterna har rensats."

---

## View ID Registry

| View ID | Type | Description |
|---------|------|-------------|
| `view-home` | index | Start screen with situation picker |
| `view-akut` | situation | Sjuk nu (dag 1–14) |
| `view-ofta` | situation | Sjuk ofta i korta perioder |
| `view-lang` | situation | Långtidssjuk / rehab |
| `view-orolig` | situation | Orolig för någon |
| `view-blanketter` | index | All forms listing |
| `form-halsosamtal` | form | Hälsosamtal |
| `form-rehabplan` | form | Rehabiliteringsplan (Tre) |
| `form-fk7459` | form | FK 7459 — Plan för återgång |
| `form-avslut` | form | Avslutssamtal |
| `form-forstadag` | form | Förstadagsintyg |
| `form-paminnelse` | form | Påminnelse om medverkan |

## CSS Contract

### View Visibility
```css
.view { display: none; }
.view.active { display: block; }
```

### Role Chips
```css
.who.w-chef  → Orange tint (manager)
.who.w-hr    → Grey tint (HRBP)
.who.w-emp   → Light grey (employee)
.who.w-ext   → White with border (external: FK, Payroll)
```

### Action Boxes
```css
.dugor → Orange-tinted box with imperative action items
.bginfo → Muted grey text for background/legal info
```
