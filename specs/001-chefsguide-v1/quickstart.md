# Quickstart: Tre Sjukfrånvaro Chefsguide v1

**Feature**: 001-chefsguide-v1 | **Date**: 2026-05-21

## Prerequisites

- Any modern browser (Chrome 90+, Edge 90+, Firefox 90+)
- A text editor (VS Code recommended)
- No build tools, no package manager, no server required

## Running the Tool

```
# Option 1: Open directly in browser
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux

# Option 2: Local HTTP server (for stricter CSP testing)
python -m http.server 8000
# Then open http://localhost:8000
```

## Project Structure

```
sjukfranvaro-rehabilitering/
├── index.html           # The entire application (HTML + CSS + JS)
├── specs/
│   └── 001-chefsguide-v1/
│       ├── spec.md      # Feature specification
│       ├── plan.md      # Implementation plan (this feature)
│       ├── research.md  # Architecture decisions
│       ├── data-model.md
│       ├── quickstart.md  # This file
│       └── contracts/
│           └── navigation.md
└── .specify/            # Speckit tooling
```

## Editing Guide

### Adding a new situation view

1. Add a `<div class="view" id="view-{slug}">` inside `.stage`
2. Include `.vhead` with `.vtag` and `<h1>`
3. Add `.involve-bar` with role chips
4. Add `.flow` with `.step` cards containing `.dugor` action boxes
5. Add a button to `view-home` `.scen-grid` calling `go('view-{slug}')`

### Adding a new form

1. Add a `<div class="view" id="form-{slug}">` inside `.stage`
2. Wrap in `.formwrap` with `.form-tophead` (includes Rensa + Exportera buttons)
3. Add `.autosave-note`
4. Add `.sheet` with `data-form="{slug}"` attribute
5. All inputs must have a `name` attribute (used as storage key)
6. Add a button to `view-blanketter` grid calling `go('form-{slug}')`

The form system will automatically:
- Autosave on any input event
- Restore data when the view is activated
- Clear data on export (afterprint)

### Modifying CSS

All CSS is in the `<style>` block (lines 7–210 approx). Key patterns:
- Brand colours: `:root` custom properties
- Components: flat class names (`.step`, `.dugor`, `.bginfo`)
- Responsive: `@media(max-width:620px)` / `@media(max-width:600px)` / `@media(max-width:560px)`
- Print: `@media print` block at end of style section

### Key constraints (from constitution)

- **Never** add external script/link tags
- **Never** reference Google Fonts or any CDN
- **Never** make network calls
- **Never** add Falck Healthcare content
- **Never** add FK 90/180/365 milestones
- **Never** add a global roles table
- **Never** use anchor-scroll navigation
- **Always** route FHV activation through HRBP
- **Always** clear form data on export
- **Always** provide a manual Rensa button on forms

## Testing Checklist (Manual)

1. **Offline**: Disconnect network → open file → verify all views work
2. **Navigation**: Click all situations → verify back button → verify home
3. **Forms**: Fill data → navigate away → return → verify restored
4. **Export**: Fill form → Export → verify print preview → verify data cleared
5. **Clear**: Fill form → Rensa → verify fields empty
6. **Privacy**: Open DevTools → Application → localStorage → verify only `tre_sjuk_v3_*` keys exist and disappear after export
7. **Network**: DevTools → Network tab → verify zero requests
