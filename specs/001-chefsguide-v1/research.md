# Research: Tre Sjukfrånvaro Chefsguide v1

**Feature**: 001-chefsguide-v1 | **Date**: 2026-05-21

## Research Tasks

### R-1: Single file vs. split architecture

**Decision**: Single HTML file (index.html)

**Rationale**: Maximum distribution simplicity — the tool is shared via email, SharePoint, or USB. A single file requires no server, no folder structure, and opens directly in any browser. The constitution permits up to 3 files but does not mandate splitting.

**Alternatives considered**:
- 3-file split (index.html + style.css + app.js): Better for maintainability and editor tooling (syntax highlighting, linting). Rejected for v1 because distribution as a folder increases friction. Remains an open question for v2.

---

### R-2: Navigation pattern

**Decision**: Array-based history stack (`navStack[]`) with `go(id)`, `back()`, `home()` functions

**Rationale**: Simplest possible implementation for a finite set of views (<15). No routing library needed. Back navigation is trivial via `pop()`. Home resets to `['view-home']`.

**Alternatives considered**:
- Hash-based routing (`#view-akut`): Allows browser back button support. Rejected because it adds complexity and the tool is not a URL-shared web app — deep linking has no value.
- State machine: Overkill for linear/tree navigation with no guards or transitions.

---

### R-3: Form persistence and privacy

**Decision**: localStorage with prefix `tre_sjuk_v3_`, auto-clear on `afterprint` event

**Rationale**: Balances usability (don't lose draft data) with GDPR compliance (health data never persists beyond the session intent). The `afterprint` event fires whether the user prints or cancels, ensuring clearance in both cases.

**Alternatives considered**:
- sessionStorage: Clears on tab close — too aggressive for a tool where managers may switch tabs.
- No persistence: Unacceptable UX — forms can have 15+ fields.
- IndexedDB: Unnecessary complexity for simple key-value form data.

---

### R-4: Export mechanism

**Decision**: `window.print()` → browser print dialog → "Save as PDF"

**Rationale**: Zero-dependency PDF generation. Every target browser supports print-to-PDF natively. Print CSS ensures clean layout. No server-side rendering or PDF library needed.

**Alternatives considered**:
- jsPDF / html2canvas: Adds 200KB+ dependency. Violates "no libraries" constitution rule.
- Server-side PDF generation: Violates "no network calls" and offline requirement.

---

### R-5: FK 7459 compliance

**Decision**: All 5 official sections with sub-headings implemented. Section 3.b uses dynamic JavaScript rendering.

**Rationale**: The FK 7459 form structure is prescribed by Försäkringskassan. All headings must be present for the document to be accepted. The 11-item measures table in 3.b is generated from a JS array to avoid HTML bloat.

**Alternatives considered**:
- Static HTML for 3.b: 100+ extra lines of repetitive markup. Rejected for maintainability.
- Linking to FK's own PDF: Violates offline requirement and doesn't integrate with the tool's workflow.

---

### R-6: CSS architecture

**Decision**: Single `<style>` block with CSS custom properties (`:root` vars), component-scoped class naming, no methodology (BEM/OOCSS)

**Rationale**: At ~200 lines of CSS, formal methodology adds ceremony without value. Custom properties enforce brand consistency. Class naming follows semantic patterns (`.step`, `.dugor`, `.who`).

**Alternatives considered**:
- External CSS file: Possible within constitution (max 3 files). Rejected for v1 single-file simplicity.
- CSS-in-JS: Not applicable (no JS framework).

---

### R-7: Textarea auto-grow

**Decision**: JavaScript `autoGrow(el)` function that sets `height: auto` then `height: scrollHeight + 2`

**Rationale**: CSS-only auto-resize (using `field-sizing: content`) lacks browser support in target versions. The JS approach works everywhere and is called on input events and view changes.

**Alternatives considered**:
- Fixed-height textareas with scroll: Poor UX for forms that will be printed.
- `contenteditable` divs: Complex to serialize for localStorage and print.

## Summary

All research items resolved. No NEEDS CLARIFICATION remaining. The prototype implements sound architectural decisions appropriate for the project's constraints (offline, single-file, zero-dependency, GDPR-compliant).
