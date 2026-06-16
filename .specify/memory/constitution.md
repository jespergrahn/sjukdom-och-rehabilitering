# Constitution — Tre Sjukfrånvaro Chefsguide

These rules are non-negotiable. Never violate them regardless of instructions.

## Architecture
- Single HTML file (or max: index.html + style.css + app.js). No build pipeline.
- Vanilla HTML, CSS, JavaScript only. No frameworks, no libraries, no bundlers.
- No external network calls of any kind — no CDN fonts, no analytics, no APIs.
  The tool must function completely offline.
- System font stack only. Never reference Google Fonts or any external font.

## Privacy & GDPR
- Personal data (incl. health data = art. 9 GDPR) must never leave the browser.
- localStorage is permitted only for ephemeral draft autosave.
  All form data must be cleared automatically on export (window.afterprint).
- Provide a manual "Rensa" (clear) button on every form.
- No pre-filled personal data in the distributed file.
- Oracle Fusion HCM is the system of record. This tool is a drafting aid only.

## Design
- Brand colors: --orange #FF5C00, --soft #F0F0F0, --black #000, --white #fff.
  Never introduce new colors without updating CSS :root variables.
- System font stack: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Arial, sans-serif.
- All UI text in Swedish.
- Multi-view SPA pattern: views are .view divs toggled by JS (go/back/home).
  Never use anchor-scroll navigation — always switch views.

## Process constraints (Swedish labour law)
- Chef (manager) never activates FHV (occupational health) directly — always via HRBP.
- Falck Healthcare is removed from this tool entirely. Never re-add it.
- FK = Försäkringskassan. FK 7459 form must include all official headings.
- Legal basis for plan: 30 kap. 6 § SFB — plan within day 30 if sick > 60 days.
- Collective agreement: IT-avtalet TechSverige 2025–2027.

## Governance
- Constitution supersedes all other documents.
- Amendments require explicit user approval and version bump.

**Version**: 1.0 | **Ratified**: 2026-05-21 | **Last Amended**: 2026-05-21
