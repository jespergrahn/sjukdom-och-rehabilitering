# Feature Specification: Tre Sjukfrånvaro Chefsguide v1

**Feature Branch**: `001-chefsguide-v1`

**Created**: 2026-05-21

**Status**: APPROVED

**Input**: Offline manager's guide for handling sickness absence and rehabilitation at Tre — single HTML file with situation views and fillable forms.

## User Scenarios & Testing

### User Story 1 - Manager Identifies Situation and Gets Guidance (Priority: P1)

A line manager has an employee who is sick or showing early warning signs. The manager opens the tool, sees a list of situations, selects the one matching their case, and immediately receives clear, step-by-step actions to take — without needing to scroll through irrelevant information or call HRBP for routine steps.

**Why this priority**: This is the core value proposition. If the manager cannot quickly find and follow the right process, the tool has no purpose.

**Independent Test**: Can be fully tested by opening the tool, selecting any situation view, and verifying that actionable steps are displayed within 2 clicks. Delivers immediate guidance value.

**Acceptance Scenarios**:

1. **Given** the manager opens the tool, **When** they see the start screen, **Then** they see a clear list of situations (Sjuk nu, Ofta sjuk, Långtidssjuk/rehab, Orolig för någon, Blanketter) with no more than one click required to reach any situation view.
2. **Given** the manager selects "Sjuk nu (dag 1–14)", **When** the view loads, **Then** they see imperative action steps in a prominent "Du gör det här" box covering notification, sick pay, and certificate requirements for day 8.
3. **Given** the manager is viewing a situation, **When** HRBP involvement is relevant, **Then** HRBP is shown as an inline role chip — never in a separate global table.
4. **Given** the manager is viewing any situation, **When** FHV (occupational health) activation is mentioned, **Then** it always states that the manager contacts HRBP who activates FHV — the manager never contacts FHV directly.

---

### User Story 2 - Manager Fills and Exports a Form (Priority: P1)

A manager needs to document a health talk, create a rehabilitation plan, or produce an official FK 7459 form. They navigate to the relevant form, fill in the fields, and export it as a printable PDF. After export, all personal/health data is automatically cleared from the browser.

**Why this priority**: Forms are the primary output artifact of the tool. Without working forms with privacy-safe export, the tool cannot fulfil its documentation purpose.

**Independent Test**: Can be tested by opening any form, entering data, clicking Export, verifying PDF output contains entered data, and confirming all fields are cleared after the print dialog closes.

**Acceptance Scenarios**:

1. **Given** the manager navigates to a form (e.g. Hälsosamtal), **When** the form loads, **Then** all required fields are visible and fillable with appropriate input types.
2. **Given** the manager has filled in form fields and leaves the page, **When** they return to the same form before exporting, **Then** their draft data is restored from localStorage autosave.
3. **Given** the manager clicks "Exportera" on a completed form, **When** the browser print dialog opens, **Then** the print preview shows a clean, professional layout with all entered data.
4. **Given** the manager completes or cancels the print dialog, **When** the afterprint event fires, **Then** all form fields are cleared and localStorage data for that form is removed.
5. **Given** the manager wants to clear a form manually, **When** they click the "Rensa" button, **Then** all fields and localStorage data for that form are cleared.

---

### User Story 3 - Manager Handles Repeated Short Absence (Priority: P2)

A manager notices an employee has been frequently absent for short periods. They select "Ofta sjuk" and receive guidance on how to initiate a health talk, when to consider a first-day certificate, and how to escalate via HRBP.

**Why this priority**: Repeated absence is a common pattern that managers handle poorly without guidance, leading to missed early intervention opportunities.

**Independent Test**: Can be tested by selecting "Ofta sjuk", verifying the action steps include health talk initiation and first-day certificate consideration.

**Acceptance Scenarios**:

1. **Given** the manager selects "Ofta sjuk", **When** the view loads, **Then** they see clear actions: initiate health talk, consider first-day certificate agreement, and when to involve HRBP.
2. **Given** the manager is in "Ofta sjuk" view, **When** they need to document a health talk, **Then** they can navigate directly to the Hälsosamtal form.

---

### User Story 4 - Manager Creates Rehabilitation Plan by Day 30 (Priority: P2)

A manager has an employee who has been sick for an extended period. They select "Långtidssjuk / rehab" and are guided through creating a rehabilitation plan (required by day 30 if absence expected > 60 days), follow-up procedures, and formal close.

**Why this priority**: The day-30 rule is a legal obligation (30 kap. 6 § SFB). Missing this deadline creates compliance risk.

**Independent Test**: Can be tested by selecting "Långtidssjuk / rehab", verifying the day-30 requirement is prominently communicated, and navigating to the Rehabiliteringsplan form.

**Acceptance Scenarios**:

1. **Given** the manager selects "Långtidssjuk / rehab", **When** the view loads, **Then** the day-30 rule for rehabilitation plan is prominently stated as an action item.
2. **Given** the manager needs to create a formal plan, **When** they navigate to the Rehabiliteringsplan form, **Then** they see a two-column task table layout without signature fields.
3. **Given** the manager needs to submit an official plan to Försäkringskassan, **When** they navigate to FK 7459, **Then** all official FK headings are present.

---

### User Story 5 - Manager Acts on Early Warning Signs (Priority: P3)

A manager is concerned about an employee's wellbeing before any sick leave has started. They select "Orolig för någon" and receive guidance on how to have an early conversation and what steps to take.

**Why this priority**: Proactive intervention prevents absence, but it's the least urgent scenario since no legal deadline is involved.

**Independent Test**: Can be tested by selecting "Orolig för någon" and verifying actionable early-intervention guidance is displayed.

**Acceptance Scenarios**:

1. **Given** the manager selects "Orolig för någon", **When** the view loads, **Then** they see concrete steps for initiating a supportive conversation before sick leave occurs.
2. **Given** the manager wants to document the conversation, **When** they look for a form link, **Then** they can navigate to Hälsosamtal.

---

### User Story 6 - Manager Formally Closes Rehabilitation (Priority: P2)

A manager has completed a rehabilitation process and needs to formally document its conclusion. They navigate to the Avslutssamtal form, record the measures taken and outcome, and export the document.

**Why this priority**: Formal close is a legal hygiene step — without it, the rehabilitation remains technically open and liability unclear.

**Independent Test**: Can be tested by navigating to the Avslutssamtal form, filling in fields, verifying the three outcome options (ordinarie arbete, kvarstående anpassning, stadigvarande nedsatt förmåga), and exporting.

**Acceptance Scenarios**:

1. **Given** the manager navigates to "Avslutssamtal", **When** the form loads, **Then** they see sections for measures taken, results, and a three-option assessment of return outcome.
2. **Given** the manager has completed the form, **When** they export, **Then** the document shows the HRBP field and signature lines for chef and medarbetare.

---

### User Story 7 - Manager Documents Non-Participation in Rehab (Priority: P3)

A manager has an employee who is not cooperating with their rehabilitation. Together with HRBP, the manager produces a written reminder clarifying the employee's obligation to participate. This form is always used with HRBP support.

**Why this priority**: This is a rare escalation scenario but legally significant — documenting non-participation protects the employer's position if the case escalates.

**Independent Test**: Can be tested by navigating to "Påminnelse om medverkan", verifying the HRBP-required callout is prominent, filling in the form, and exporting.

**Acceptance Scenarios**:

1. **Given** the manager navigates to "Påminnelse om medverkan", **When** the form loads, **Then** a prominent callout states that HRBP must always be involved.
2. **Given** the form is filled in, **When** the manager exports, **Then** the document includes fields for chef, HRBP, and medarbetare acknowledgement.

---

### User Story 8 - Tool Works Completely Offline (Priority: P1)

The tool must load and function entirely without internet connectivity. No external resources are fetched, no data leaves the browser, and all functionality works from the local HTML file.

**Why this priority**: This is a hard architectural requirement driven by privacy (health data / GDPR art. 9) and deployment simplicity (distributed as a file).

**Independent Test**: Can be tested by disconnecting from the network, opening the HTML file, and verifying all views and forms function correctly.

**Acceptance Scenarios**:

1. **Given** the device has no internet connection, **When** the manager opens the HTML file, **Then** the tool loads completely with all styles and functionality.
2. **Given** the tool is running, **When** network traffic is monitored, **Then** zero external requests are made (no CDN, no analytics, no API calls).
3. **Given** form data is entered, **When** inspecting browser storage, **Then** data exists only in localStorage with the tool's prefix and never in cookies or sent to any server.

---

### Edge Cases

- What happens when localStorage is full or disabled? Form autosave fails silently; the user can still fill and export forms without autosave.
- What happens when the user navigates away from a half-filled form and returns? Draft data is restored from localStorage if available.
- What happens when the print dialog is cancelled (not printed)? Form data is still cleared on afterprint for privacy — the export action itself triggers the clear.
- What happens when multiple forms are open in different tabs? Each tab maintains its own state; localStorage uses form-specific keys with the shared prefix.
- What happens when the browser does not support window.print()? The "Exportera" button should be disabled or hidden in browsers without print support.

## Requirements

### Functional Requirements

- **FR-001**: System MUST present a start screen with clearly labelled situation categories that each lead to a dedicated view in one click.
- **FR-002**: System MUST implement a multi-view SPA pattern where only one view is visible at a time (active class = display:block, others display:none).
- **FR-003**: System MUST provide go(id), back(), and home() navigation functions with a navStack array for history.
- **FR-004**: System MUST call window.scrollTo(0,0) and autoGrow() on every view change.
- **FR-005**: Each situation view MUST contain a "Du gör det här" action box with imperative steps for the manager.
- **FR-006**: Each situation view MAY contain a ".bginfo" section with muted background/legal information below the action box.
- **FR-007**: System MUST show HRBP involvement as inline role chips (.who.w-hr) within situation content — never in a global roles table.
- **FR-008**: System MUST never show the manager contacting FHV (occupational health) directly — always via HRBP.
- **FR-009**: System MUST provide six fillable forms: Hälsosamtal, Rehabiliteringsplan, FK 7459, Avslutssamtal, Förstadagsintyg, Påminnelse om medverkan.
- **FR-010**: Each form MUST use a data-form attribute wrapper and name attributes on inputs as storage keys with localStorage prefix "tre_sjuk_v3_".
- **FR-011**: System MUST autosave form data to localStorage on input change.
- **FR-012**: System MUST restore form data from localStorage when a form view is activated (restoreForm).
- **FR-013**: System MUST export forms via window.print() producing a clean printable layout.
- **FR-014**: System MUST clear all form data (fields and localStorage) automatically on the afterprint event (clearForm).
- **FR-015**: Each form MUST have a manual "Rensa" button that clears all fields and associated localStorage.
- **FR-016**: System MUST NOT make any external network calls — no CDN, fonts, analytics, or APIs.
- **FR-017**: System MUST use only system font stack and CSS custom properties for brand colors.
- **FR-018**: System MUST NOT include any Falck Healthcare references.
- **FR-019**: System MUST NOT include FK day-90/180/365 milestones.
- **FR-020**: System MUST NOT include a global roles table.
- **FR-021**: System MUST NOT use anchor-jump/scroll navigation.
- **FR-022**: The FK 7459 form MUST include all official Försäkringskassan headings.
- **FR-023**: The Rehabiliteringsplan form MUST use a two-column task table layout without signature fields.
- **FR-024**: System MUST call autoGrow(el) on all visible textareas after navigation to ensure proper sizing.
- **FR-025**: All UI text MUST be in Swedish.
- **FR-026**: The "Långtidssjuk / rehab" view MUST prominently state the day-30 rule (30 kap. 6 § SFB).

### Key Entities

- **Situation View**: A screen presenting one specific absence scenario (e.g. "Sjuk nu dag 1–14") with action steps and background info. Key attributes: id, title, action content, background info, relevant form links.
- **Form**: A fillable document template for a specific HR process (e.g. Hälsosamtal, FK 7459). Key attributes: form id, field definitions, localStorage key prefix, export layout.
- **Navigation Stack**: The ordered history of visited views enabling back-navigation. Managed as an array (navStack) with push/pop semantics.
- **Draft Autosave**: Ephemeral localStorage entries holding form field values between sessions. Automatically cleared on export.

## Success Criteria

### Measurable Outcomes

- **SC-001**: A manager with no prior training can open the tool and reach the correct situation view for their case within 2 clicks and 10 seconds.
- **SC-002**: A manager can complete and export any form within 5 minutes of starting to fill it in.
- **SC-003**: 100% of form exports result in complete data clearance from the browser (zero residual personal data in localStorage after export).
- **SC-004**: The tool loads and functions fully with zero network connectivity — verified by zero outbound requests.
- **SC-005**: Every situation view contains at least one concrete imperative action ("Du gör:") that the manager can execute immediately.
- **SC-006**: HRBP-related escalation advice reduces routine HRBP queries from managers by providing self-service guidance for standard steps.
- **SC-007**: 100% of situation views correctly route FHV activation through HRBP — the manager is never instructed to contact FHV directly.

## Assumptions

- Managers use desktop or laptop computers with modern browsers (Chrome, Edge, Firefox) — mobile layout is out of scope.
- The tool is distributed as a single file (or small file set) via internal channels (e.g. SharePoint, email) — no web server deployment.
- Managers have basic computer literacy but minimal HR/legal expertise.
- Oracle Fusion HCM remains the system of record; this tool is a drafting aid only.
- The IT-avtalet TechSverige 2025–2027 collective agreement applies to all employees covered by the tool.
- Swedish is the only required language.
- No authentication or user identification is needed — the tool contains no pre-filled personal data.
- localStorage is available in the target browsers; if unavailable, autosave degrades gracefully but forms still function.

## Out of Scope

- Mobile-optimised layout
- Multi-language support
- Backend, authentication, or server-side storage
- Direct document submission to Försäkringskassan or Oracle Fusion
- Medical or diagnostic information (tool covers work capacity only)
- Accessibility audit and ARIA improvements (planned for Phase 2)
- Print styles for situation views (planned for Phase 2)
- Workplace accident / psykisk ohälsa scenarios (planned for Phase 3)

## Open Questions

- Should the single HTML file be split into three files (index.html + style.css + app.js) for maintainability, while staying within the constitution's "max 3 files" rule?
- Should Phase 4 Oracle Fusion integration be a separate product or an extension of this tool?
- Who owns version updates when IT-avtalet or SFB legislation changes?
