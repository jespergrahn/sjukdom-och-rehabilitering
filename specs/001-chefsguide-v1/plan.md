# Implementation Plan: Tre Sjukfrånvaro Chefsguide v1

**Branch**: `001-chefsguide-v1` | **Date**: 2026-05-21 | **Spec**: [specs/001-chefsguide-v1/spec.md](spec.md)

**Input**: Feature specification from `/specs/001-chefsguide-v1/spec.md`

**Note**: This plan documents architecture decisions already made in the prototype (index.html) and identifies remaining gaps within v1 scope.

## Summary

Offline manager's guide for handling sickness absence and rehabilitation at Tre. Single HTML file implementing a multi-view SPA with situation-specific guidance and fillable forms that export to PDF with automatic data clearance. The prototype is fully built — this plan captures the as-built architecture and identifies quality gaps.

## Technical Context

**Language/Version**: HTML5, CSS3, ES5 JavaScript (vanilla, no transpilation)

**Primary Dependencies**: None — zero external dependencies

**Storage**: Browser localStorage (ephemeral draft autosave only, prefix `tre_sjuk_v3_`)

**Testing**: Manual browser testing (Chrome, Edge, Firefox). No automated test framework in v1 scope.

**Target Platform**: Desktop browsers (Chrome 90+, Edge 90+, Firefox 90+) via file:// or local HTTP

**Project Type**: Single-page HTML application (offline tool, distributed as a file)

**Performance Goals**: Instant load (<100ms, single file, no network). Any situation reachable in ≤2 clicks and <10 seconds.

**Constraints**: Zero network calls, completely offline, single HTML file (~960 lines), GDPR art. 9 compliant (health data never leaves browser)

**Scale/Scope**: 5 situation views, 6 forms, 1 home view, 1 forms index view — total 13 views

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Rule | Status | Evidence |
|------|--------|----------|
| Single HTML file (max 3 files) | ✅ PASS | `index.html` only |
| Vanilla HTML/CSS/JS only | ✅ PASS | No imports, no build tools |
| No external network calls | ✅ PASS | No CDN, fonts, analytics, APIs |
| System font stack | ✅ PASS | `--ui` var uses system fonts |
| localStorage for ephemeral drafts only | ✅ PASS | `saveForm`/`restoreForm`/`clearForm` pattern |
| Auto-clear on export (afterprint) | ✅ PASS | `window.addEventListener('afterprint', ...)` |
| Manual "Rensa" on every form | ✅ PASS | All 6 forms have Rensa button |
| Brand colors via CSS :root | ✅ PASS | `--orange`, `--soft`, `--black`, `--white` |
| All UI text in Swedish | ✅ PASS | Verified |
| Multi-view SPA (go/back/home + navStack) | ✅ PASS | `navStack[]`, `go()`, `back()`, `home()` |
| No anchor-scroll navigation | ✅ PASS | Views toggled via `.active` class |
| Chef never contacts FHV directly | ✅ PASS | Always "via HRBP" in all views |
| No Falck Healthcare references | ✅ PASS | Not present |
| FK 7459 all official headings | ✅ PASS | Sections 1–5 with sub-headings |
| No FK 90/180/365 milestones | ✅ PASS | Not present |
| No global roles table | ✅ PASS | Inline `.who` chips only |
| HRBP as inline role chips | ✅ PASS | `.who.w-hr` pattern used |
| Oracle Fusion is system of record | ✅ PASS | Noted in privacy box and form notes |
| 30 kap. 6 § SFB day-30 rule | ✅ PASS | Prominently stated in `view-lang` |
| IT-avtalet TechSverige 2025–2027 | ✅ PASS | Referenced in footer |

**Gate result: ALL PASS** — no violations.

## Project Structure

### Documentation (this feature)

```text
specs/001-chefsguide-v1/
├── plan.md              # This file
├── research.md          # Phase 0 output - architecture decisions
├── data-model.md        # Phase 1 output - entities and relationships
├── quickstart.md        # Phase 1 output - development guide
├── contracts/           # Phase 1 output - UI contracts
│   └── navigation.md   # Navigation API contract
└── checklists/
    └── requirements.md  # Spec quality checklist (pre-existing)
```

### Source Code (repository root)

```text
index.html               # Single-file application (HTML + CSS + JS)
```

**Structure Decision**: Single HTML file chosen for maximum distribution simplicity (copy/email/SharePoint). All styles are inlined in `<style>`, all JavaScript in `<script>`. The constitution permits up to 3 files (index.html + style.css + app.js) but the current v1 keeps everything in one file. This is an open question for future maintainability but does not block v1.

## Architecture Decisions (As-Built)

### 1. Navigation System
- **Pattern**: Array-based history stack (`navStack[]`)
- **Functions**: `go(id)`, `back()`, `home()`
- **View activation**: CSS class `.active` → `display:block`, all others `display:none`
- **Scroll reset**: `window.scrollTo(0,0)` on every view change
- **Textarea resize**: `autoGrow()` called on visible textareas after navigation

### 2. Form System
- **Structure**: `<div data-form="formId">` wrapper per form
- **Storage keys**: `name` attribute on inputs → localStorage key
- **Prefix**: `tre_sjuk_v3_`
- **Lifecycle**: `saveForm(id)` → `restoreForm(id)` → `exportForm(id)` → `clearForm(id)`
- **Export**: `window.print()` triggers print dialog; `afterprint` event clears data
- **Graceful degradation**: `storageOK()` check; if localStorage unavailable, autosave silently disabled

### 3. Situation Views
- **Pattern**: Timeline-based flow with `.step` cards (when/what)
- **Actions**: `.dugor` boxes with imperative steps ("Du gör det här")
- **Background**: `.bginfo` sections for legal/contextual info
- **Roles**: `.who.w-{role}` inline chips (never a global table)
- **Checklists**: Expandable accordion with tick-to-complete items (ephemeral, not persisted)

### 4. FK 7459 Dynamic Content
- **3.b table**: Rendered by JavaScript from `fkAtgarder` array (11 measures)
- **Pattern**: Radio buttons (Ja/Nej) + free-text motivation per row
- **Rationale**: Avoids 100+ lines of repetitive HTML

### 5. Print Styles
- **Approach**: `@media print` block hides topbar, buttons, autosave notes
- **Layout**: Removes decorative styling for clean PDF output
- **Page margin**: 1.4cm all sides

## Identified Gaps (v1 Scope)

| # | Gap | Impact | Recommendation |
|---|-----|--------|----------------|
| G-1 | No `autoGrow()` call in `render()` for restored form textareas on navigation | Textareas may appear collapsed when navigating back to a form with restored data | Add `restoreForm` + `autoGrow` call in `render()` when entering a form view |
| G-2 | Checklists (tick items) are not persisted to localStorage | Progress lost on page reload | Acceptable for v1 — checklists are ephemeral by design (constitution: localStorage for forms only) |
| G-3 | No browser-support check or fallback for `window.print()` | Edge case per spec: button should be disabled in unsupported browsers | Add feature detection: `if(!window.print) hide export buttons` |
| G-4 | `restoreForm` radio button logic has a bug — checks `data[el.name]!==undefined` when `el.name` may not be in `data` | Radio buttons may not restore correctly in edge cases | Fix conditional logic in restoreForm |
| G-5 | No skip-link or landmark roles | Accessibility planned for Phase 2 | Out of scope for v1 |
| G-6 | No explicit `<meta>` viewport lock for print | Print may vary across browsers | Low impact — current print styles adequate |

## Complexity Tracking

> No constitution violations — this section is empty.

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| — | — | — |
