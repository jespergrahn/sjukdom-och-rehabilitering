# Data Model: Tre Sjukfrånvaro Chefsguide v1

**Feature**: 001-chefsguide-v1 | **Date**: 2026-05-21

## Entities

### 1. View

A screen the user can navigate to. All views are `<div class="view" id="{viewId}">`.

| Field | Type | Description |
|-------|------|-------------|
| id | string | Unique DOM id (e.g. `view-akut`, `form-halsosamtal`) |
| type | enum | `situation` \| `form` \| `index` |
| title | string | Swedish display title |
| tag | string | Category label shown as `.vtag` eyebrow |
| active | boolean | Whether this view is currently displayed |

**Naming convention**:
- Situation views: `view-{slug}` (e.g. `view-akut`, `view-ofta`, `view-lang`, `view-orolig`)
- Form views: `form-{slug}` (e.g. `form-halsosamtal`, `form-rehabplan`, `form-fk7459`)
- Index views: `view-{slug}` (e.g. `view-home`, `view-blanketter`)

### 2. Form

A fillable document template wrapped in `<div data-form="{formId}">`.

| Field | Type | Description |
|-------|------|-------------|
| formId | string | Identifier used for localStorage key and DOM query |
| fields | Field[] | List of input/textarea elements with `name` attributes |
| storageKey | string | `tre_sjuk_v3_{formId}` — localStorage key |
| hasExport | boolean | Always true — all forms have export |
| hasClear | boolean | Always true — all forms have manual clear |

**Instances**:
| formId | Title | Field count |
|--------|-------|-------------|
| halsosamtal | Hälsosamtal | 10 |
| rehabplan | Rehabiliteringsplan | 16 |
| fk7459 | FK 7459 — Plan för återgång | 40+ (incl. dynamic 3.b) |
| avslut | Avslutssamtal | 9 |
| forstadag | Förstadagsintyg | 8 |
| paminnelse | Påminnelse om medverkan | 7 |

### 3. Field

An individual form input element.

| Field | Type | Description |
|-------|------|-------------|
| name | string | Storage key (unique within form) |
| type | enum | `text` (input) \| `textarea` \| `checkbox` \| `radio` |
| label | string | Swedish label text |
| value | string \| boolean | Current user-entered value |

### 4. Navigation Stack

The ordered history of visited views.

| Field | Type | Description |
|-------|------|-------------|
| stack | string[] | Array of view IDs, last element is current |
| initial | string | Always `'view-home'` |

**Operations**:
- `go(id)`: push id → re-render
- `back()`: pop → re-render (minimum length: 1)
- `home()`: reset to `['view-home']` → re-render

### 5. Draft Autosave Entry

A localStorage record holding form state.

| Field | Type | Description |
|-------|------|-------------|
| key | string | `tre_sjuk_v3_{formId}` |
| value | JSON string | `{ fieldName: fieldValue, ... }` |
| lifecycle | enum | Created on input → restored on view enter → deleted on export/clear |

**State transitions**:
```
[Empty] → input event → [Saved]
[Saved] → view enter → [Restored]
[Saved/Restored] → export (afterprint) → [Cleared]
[Saved/Restored] → manual clear → [Cleared]
[Cleared] → input event → [Saved]
```

### 6. Situation Step

A time-sequenced action card within a situation view.

| Field | Type | Description |
|-------|------|-------------|
| when | string | Time indicator (e.g. "Dag 1", "Dag 8", "Löpande") |
| label | string | Short time label (e.g. "Anmälan", "Läkarintyg") |
| title | string | Step heading |
| actions | string[] | Imperative action items in `.dugor` |
| background | string | Optional background info in `.bginfo` |
| isWarning | boolean | Whether step has `.warn` class (orange highlight) |
| formLinks | string[] | Optional form view IDs linked from this step |

### 7. Role Chip

Inline indicator of who is involved in a situation.

| Field | Type | Description |
|-------|------|-------------|
| role | enum | `chef` \| `hr` \| `emp` \| `ext` |
| label | string | Display text (e.g. "Du", "HRBP", "Medarbetare") |
| cssClass | string | `.w-chef` \| `.w-hr` \| `.w-emp` \| `.w-ext` |

## Relationships

```
View (index: view-home)
 └── links to → View (situation: view-akut, view-ofta, ...)
                  └── links to → View (form: form-halsosamtal, ...)
                                    └── contains → Form
                                                    └── contains → Field[]
                                                    └── persists as → Draft Autosave Entry

View (index: view-blanketter)
 └── links to → View (form: all 6 forms)

Navigation Stack
 └── tracks → View[] (ordered history)
```

## Validation Rules

| Entity | Rule | Source |
|--------|------|--------|
| Form field (pnr) | 12-digit Swedish personal number format | FK 7459 requirement |
| Navigation | Stack minimum length = 1 (always has home) | FR-003 |
| Draft Autosave | Key must use prefix `tre_sjuk_v3_` | FR-010 |
| Draft Autosave | Must be cleared on afterprint event | FR-014, constitution |
| Form | Must have both Rensa and Exportera buttons | FR-015, constitution |
| View | Only one view may have `.active` class at a time | FR-002 |
