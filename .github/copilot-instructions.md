<!-- SPECKIT START -->
For additional context about technologies to be used, project structure,
shell commands, and other important information, read the current plan
at specs/001-chefsguide-v1/plan.md
<!-- SPECKIT END -->

## Behaviour
- Always read the constitution at .specify/memory/constitution.md before making changes.
- Prefer small, focused edits. Do not reorganise views or rename JS functions unprompted.
- Preserve existing patterns exactly. When in doubt, follow what already exists.
- Do not suggest frameworks, libraries, or build tools. Vanilla only.

## Navigation system
- Views: <div class="view" id="view-xxx"> or <div class="view" id="form-xxx">
- Active view has class `active` (display:block). All others display:none.
- Navigation: go(id), back(), home() with navStack[] array.
- Always call window.scrollTo(0,0) and autoGrow() on view change.

## Form system
- Each form is a <div data-form="formid"> wrapper.
- Inputs use `name` attribute as storage key. localStorage prefix: "tre_sjuk_v3_"
- saveForm / restoreForm / clearForm / exportForm are the four core functions.
- Export = window.print() → afterprint → clearForm.
- autoGrow(el) must be called on all visible textareas after navigation.

## Swedish process rules
- Chef is the primary user. Every step in a situation view must be actionable.
- Action content goes in .dugor ("Du gör det här") boxes.
- Background/legal info goes in .bginfo (muted, below the action box).
- HRBP involvement: always show as inline .who.w-hr chip, never in a global table.

## What NOT to do
- Never add Falck Healthcare references.
- Never add FK dag 90/180/365 milestones.
- Never add a global roles table.
- Never add anchor-jump navigation.
- Never add external font or script tags.
