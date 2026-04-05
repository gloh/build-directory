# Motion Design System

Complete reference for Motion's design language, token architecture, component patterns, and operational rules for Claude Code.

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Design Principles](#2-design-principles)
3. [Token Architecture](#3-token-architecture)
4. [Color System](#4-color-system)
5. [Typography](#5-typography)
6. [Spacing](#6-spacing)
7. [Components](#7-components)
8. [Patterns](#8-patterns)
9. [States & Feedback](#9-states--feedback)
10. [Accessibility](#10-accessibility)
11. [Data Pair Patterns](#11-data-pair-patterns)
12. [Token Reference — Brand (Tier 1)](#12-token-reference--brand-tier-1)
13. [Token Reference — Alias (Tier 2)](#13-token-reference--alias-tier-2)
14. [Token Reference — Mapped (Tier 3)](#14-token-reference--mapped-tier-3)
15. [Token Reference — Spacing](#15-token-reference--spacing)
16. [Token Reference — Typography](#16-token-reference--typography)
17. [Claude Code Operational Rules](#17-claude-code-operational-rules)
18. [Quick Reference — Key Tokens](#18-quick-reference--key-tokens)

---

## 1. Project Overview

Motion's Design System is the single source of truth for all UI and interaction patterns across the product. It exists to:

- Eliminate inconsistency between features and surfaces
- Speed up design and engineering handoff
- Allow AI-assisted generation of components that stay on-brand
- Reduce design and UX debt as the product scales

### Who uses this system

| Role | How they use it |
|------|----------------|
| Product Designer | Reference for components, tokens, and principles |
| Engineer | Token values, component specs, interaction states |
| Claude (AI) | Rules for generating system-aligned prototypes and code |

### Design Philosophy

Motion is used by agents and power users managing high volumes of data. The UI must be efficient, information-dense without being overwhelming, and fast to navigate. It is not a consumer product — do not optimize for discoverability at the expense of speed.

---

## 2. Design Principles

These principles govern all design decisions. When the Design System does not specify a solution, apply the most relevant principle.

### 1. Clarity beats density

Show less. Show it clearly. The interface should be understandable at first glance without requiring users to scan everything.

**Do:** Use visual hierarchy, group related fields, use collapsible sections.
**Don't:** Turn spreadsheets into UI. Show all fields and data at once without structure.

### 2. Progressive disclosure by default

Reveal information only when it becomes relevant. Users should interact with one problem at a time.

**Do:** Use accordions, toggles, and step-based flows. Reveal complexity gradually.
**Don't:** Force users to understand everything upfront. Block advanced users from data they need.

### 3. Consistency is a feature

Consistency is not polish — it is functionality. Similar actions must look and behave the same everywhere.

**Do:** Reuse components, layouts, and interaction patterns. Keep headers, toasts, forms, and actions consistent.
**Don't:** Introduce new patterns without clear justification. Solve the same problem in different ways.

### 4. Optimize for real user roles

Design for how people actually use the product. Motion is often operated by agents doing bulk work.

**Do:** Allow bulk input and inline editing. Keep forms efficient for users who already have the data.
**Don't:** Force slow onboarding flows on experienced users. Assume the end customer is always the primary user.

### 5. Make system state visible

Users should always understand what happened, what changed, and what's next. The system should never feel like magic.

**Do:** Show totals, statuses, autosave indicators, and timestamps. Use clear success, error, and loading states.
**Don't:** Hide important feedback. Make users guess whether something worked.

### 6. Design for change, not perfection

The system must support iteration. MVPs should still respect core principles without over-engineering.

**Do:** Build flexible layouts. Prefer editable and extendable components.
**Don't:** Over-engineer one-off solutions. Lock the product into rigid flows.

---

## 3. Token Architecture

Motion uses a **3-tier token system**. Tokens flow in one direction: Brand → Alias → Mapped.

```
brand.json      →    alias.json      →    mapped.json
(raw values)         (semantic)           (component slots)

color.blue.600  →  color.primary.default  →  button.primary.bg
color.red.600   →  color.danger.default   →  button.danger.bg
space.4 (16px)  →  (direct reference)     →  input.paddingX
```

### Tier 1 — Brand (raw primitives)

Raw, primitive values. No semantic meaning. Never referenced directly in components.

- Color palettes (blue, neutral, green, red, yellow, orange)
- Font families, weights, sizes, line heights, letter spacing
- Spacing scale (0–24, in 4px steps)
- Border radius, shadows, animation durations and easings

### Tier 2 — Alias (semantic)

Semantic tokens that reference brand tokens. Give meaning to values.

- `color.primary` — main interactive color (maps to blue)
- `color.danger` — destructive actions (maps to red)
- `color.success` — confirmations (maps to green)
- `text.primary`, `text.secondary`, `text.placeholder` — text hierarchy
- `background.page`, `background.surface`, `background.overlay`
- `border.default`, `border.focus`, `border.error`

### Tier 3 — Mapped (component slots)

Component-level tokens consumed directly by engineers. Each token maps to a specific UI slot.

- `button.primary.bg`, `button.primary.bgHover`
- `input.border`, `input.borderFocus`, `input.errorText`
- `table.headerBg`, `table.rowBgHover`
- `card.bg`, `card.radius`, `card.shadow`
- `toast.success.bg`, `toast.danger.border`

### Rules

1. Never hardcode color hex values in components — always use mapped tokens.
2. Never reference brand tokens directly in components — go through alias or mapped.
3. When no mapped token exists for a slot, use an alias token.
4. When no alias token exists, escalate to a design decision — do not guess.

---

## 4. Color System

### Primary (Blue)

Used for: interactive elements, links, focus rings, CTAs.

| Token | Value | Usage |
|-------|-------|-------|
| `color.primary.default` | `#2563EB` | Button background, active states |
| `color.primary.hover` | `#1D4ED8` | Hover on primary elements |
| `color.primary.subtle` | `#EFF6FF` | Background tints for selected rows |
| `color.primary.muted` | `#DBEAFE` | Badge backgrounds, callouts |
| `color.primary.text` | `#1D4ED8` | Text on light primary backgrounds |

### Neutrals

Used for: backgrounds, borders, text hierarchy.

| Token | Value | Usage |
|-------|-------|-------|
| `background.page` | `#F9FAFB` | App background |
| `background.surface` | `#FFFFFF` | Cards, modals, sidebars |
| `border.default` | `#E5E7EB` | Card borders, dividers |
| `border.strong` | `#D1D5DB` | Input borders |
| `text.primary` | `#111827` | Body text, headings |
| `text.secondary` | `#4B5563` | Subtext, descriptions |
| `text.tertiary` | `#6B7280` | Metadata, timestamps |
| `text.placeholder` | `#9CA3AF` | Input placeholder |
| `text.disabled` | `#D1D5DB` | Disabled state text |

### Semantic Colors

| Intent | Token | Value | Usage |
|--------|-------|-------|-------|
| Success | `color.success.default` | `#16A34A` | Confirmed states |
| Danger | `color.danger.default` | `#DC2626` | Errors, destructive actions |
| Warning | `color.warning.default` | `#CA8A04` | Warnings, caution states |
| Info | `color.info.default` | `#3B82F6` | Informational messages |

---

## 5. Typography

Font: **Inter** (primary), **JetBrains Mono** (code/technical values).

### Type Scale

| Name | Size | Weight | Use |
|------|------|--------|-----|
| `displayLg` | 32px / 700 | Bold | Page-level hero titles |
| `displayMd` | 28px / 700 | Bold | Main page H1 |
| `headingLg` | 24px / 600 | Semibold | Card titles, modal headings |
| `headingMd` | 20px / 600 | Semibold | Panel headers, sub-sections |
| `headingSm` | 16px / 600 | Semibold | Form section headers |
| `bodyLg` | 16px / 400 | Regular | Readable prose |
| `bodyMd` | 14px / 400 | Regular | Most UI text (default) |
| `bodySm` | 13px / 400 | Regular | Metadata, timestamps |
| `bodyXs` | 12px / 400 | Regular | Badges, helper text |
| `label` | 14px / 500 | Medium | Form labels, column headers |
| `labelSm` | 12px / 500 | Medium | Overlines, eyebrows |
| `mono` | 13px / 400 | Regular | Code, IDs, reference numbers |

### Rules

- Default body text is always `bodyMd` (14px/400).
- Use `label` (medium weight) for all form field labels, not `bodyMd`.
- Use `mono` for any IDs, reference numbers, or numeric codes.
- Never go below `bodyXs` (12px) for any actionable text.
- Line height: use `normal` (1.5) for body, `snug` (1.35) for headings.

---

## 6. Spacing

Built on a **4px base grid**. All layout and page-level spacing must be multiples of 4. Component-level tokens may use off-grid values only for optical compensation.

### Scale Reference

| Token | Value | Common Use |
|-------|-------|-----------|
| `space.0` | 0px | No spacing |
| `space.1` | 4px | Icon-to-label gap, tight badge padding |
| `space.2` | 8px | Inline gaps, chip padding |
| `space.3` | 12px | Input padding, tight list items |
| `space.4` | 16px | Standard padding, form row gap |
| `space.5` | 20px | Section gaps |
| `space.6` | 24px | Card padding, modal padding |
| `space.8` | 32px | Major section breaks |
| `space.10` | 40px | Page header spacing |
| `space.12` | 48px | Hero-level padding |
| `space.16` | 64px | Page-level gutters |
| `space.20` | 80px | Large section separators |
| `space.24` | 96px | Marketing / landing use only |

### Layout Defaults

| Token | Value | Use |
|-------|-------|-----|
| `layout.pageMarginX` | 32px | Desktop horizontal page margin |
| `layout.pageMarginXSm` | 16px | Mobile horizontal page margin |
| `layout.pageMaxWidth` | 1280px | Maximum content width |
| `layout.contentMaxWidth` | 800px | Readable content column max width |
| `layout.sidebarWidth` | 240px | Primary sidebar |
| `layout.sidebarWidthCollapsed` | 56px | Collapsed sidebar |
| `layout.topBarHeight` | 56px | Top navigation bar |
| `layout.sectionGap` | 32px | Between major page sections |
| `layout.formRowGap` | 16px | Between form fields vertically |
| `layout.formColGap` | 16px | Between form columns horizontally |

### Component Spacing Tokens

| Token | Value | Use |
|-------|-------|-----|
| `component.iconGap` | 8px | Gap between icon and label in buttons/links |
| `component.labelGap` | **6px** | Gap between a field label and its input |
| `component.helperTextGap` | 4px | Gap between input and helper/error text |
| `component.listItemGap` | 4px | Gap between items in a list |
| `component.cardGap` | 16px | Gap between cards in a grid |
| `component.tableRowHeight` | 48px | Default table row height |
| `component.tableRowHeightSm` | 36px | Compact table row height |

**Note on `component.labelGap = 6px`**: This is an intentional off-grid value. A 4px gap reads as too tight between a label and its input; 8px reads as too loose. 6px is an optical compensation — a deliberate exception to the 4px grid rule, not a mistake. Do not "fix" it to 4px or 8px.

---

## 7. Components

### Button

Four variants: `primary`, `secondary`, `danger`, `ghost`.

Three sizes: `sm` (28px tall), `md` (36px tall), `lg` (44px tall).

**Rules:**
- One primary action per view. Use secondary for supporting actions.
- Danger buttons only for destructive, irreversible actions.
- Ghost buttons for tertiary actions inside dense UI (tables, toolbars).
- Never use a primary button inside a modal next to a cancel — use primary + secondary.
- Always include a loading state for async actions. Disable while loading.

### Input

Default height: 36px. Radius: 6px (radius.md).

States: default, hover, focus, error, disabled.

**Rules:**
- Always show a visible label above the input — never rely on placeholder as the label.
- Helper text appears below the input at all times when present.
- Error text replaces helper text on error state.
- Group related inputs using `layout.formColGap` (16px horizontal).
- Vertical gap between rows: `layout.formRowGap` (16px).

### Table / Data Grid

Used for all list views with structured data.

**Rules:**
- Default row height: 48px. Compact mode: 36px.
- Always include a column header row with `label` typography and neutral.50 background.
- Use `table.rowBgHover` on hover for scannability.
- Highlighted/selected rows use `color.blue.50` background.
- Avoid more than 8 visible columns without a horizontal scroll or column toggler.
- Show a total count in the table header or above the table.

### Card

Default: white background, `border.default` border, `shadow.sm`, `radius.lg` (8px), `space.6` (24px) padding.

**Rules:**
- Cards contain one logical unit of content. Do not mix unrelated content in a single card.
- Use cards to create visual groupings on a neutral page background.
- Avoid nesting cards more than one level deep.

### Modal / Dialog

**Rules:**
- Use for actions that interrupt the user's flow and require a decision.
- Always have a clear title, body, and action buttons.
- Actions go in the modal footer: primary action right, cancel left.
- Destructive modals must explicitly name the thing being deleted.
- Always allow closing with ESC and clicking the overlay.
- Do not use modals for forms with more than 6 fields — use a dedicated page or side panel.

### Badge / Status Pill

Used for status indicators, counts, and categorical labels.

**Rules:**
- Use semantic color variants: `neutral`, `primary`, `success`, `danger`, `warning`.
- Always use `labelSm` (12px/500) or `bodyXs` (12px/400) typography.
- Maximum 2–3 words in a badge. Never truncate badge text.

### Toast / Notification

Four types: `success`, `danger`, `warning`, `info`.

**Rules:**
- Toasts are temporary — auto-dismiss after 4–6 seconds.
- Errors may persist until dismissed by the user.
- Position: bottom-right of the viewport.
- Never stack more than 3 toasts simultaneously.
- Keep toast messages under 80 characters.

---

## 8. Patterns

### Form Layout

Two-column forms for dense data entry (agent/power user workflows). Single-column for simpler or customer-facing flows.

```
[Label]           [Label]
[Input ________]  [Input ________]

[Label]
[Textarea _________________________]

[Cancel]                   [Save]
```

- Labels always above inputs, never inline (except for toggles/checkboxes).
- Required fields: mark with asterisk (*) in the label, never use color alone.
- Submit action always at the bottom right. Cancel left of submit.

### Empty States

Every list, table, or data view must define an empty state.

Components:
- Illustration or icon (optional)
- Heading: what is empty and why
- Description: what the user can do
- CTA: primary action to add content

### Loading States

- Skeleton screens for initial page/data load.
- Spinner for inline async actions (button clicks, searches).
- Never block the full screen with a spinner unless absolutely necessary.
- Show partial data as it loads — do not wait for all data before rendering.

### Confirmation Dialogs

Use for destructive or irreversible actions only.

Template:
- Title: "Delete [thing name]?"
- Body: Brief explanation of consequence.
- Actions: "Cancel" (secondary) + "Delete" or "Confirm" (danger primary).

### Inline Editing

Preferred for power users editing data in tables or lists.

- Clicking a cell activates edit mode in place.
- Pressing Enter or Tab saves and advances.
- Pressing Escape cancels.
- Show save/discard controls only for multi-field forms, not single-field inline edits.

---

## 9. States & Feedback

Every interactive element must define all relevant states. Never leave a state unstyled.

### Interactive States

| State | Description |
|-------|-------------|
| Default | Resting state |
| Hover | Mouse over, pre-interaction |
| Focus | Keyboard focus — always visible, uses `border.focus` (`#3B82F6`) |
| Active | Being pressed/clicked |
| Disabled | Not interactive — reduce opacity or use disabled tokens |
| Loading | Async in progress |
| Error | Validation failed or action errored |
| Success | Action confirmed |

### Focus Ring

All interactive elements must have a visible focus ring for keyboard accessibility.
Use: `outline: 2px solid {border.focus}; outline-offset: 2px;`
Never remove focus outlines without replacing them.

### Error Handling

- Field-level errors: show below the input using `input.errorText` color and `bodyXs` type.
- Page-level errors: use an inline alert at the top of the form or content area.
- Action errors: use a `danger` toast.
- Never clear error messages until the user has corrected them.

---

## 10. Accessibility

- All text must meet WCAG AA contrast (4.5:1 for body text, 3:1 for large text).
- Never communicate state or meaning using color alone — always pair with text or icon.
- All interactive elements are keyboard-navigable.
- Focus order follows reading order (top to bottom, left to right).
- All images and icons used for meaning have `alt` text or `aria-label`.
- Form fields are always associated with their label via `for`/`id` or `aria-labelledby`.
- Modals trap focus while open and restore focus when closed.
- Avoid animations for users who prefer reduced motion — respect `prefers-reduced-motion`.

---

## 11. Data Pair Patterns

Motion UIs frequently display pairs of related values. These pairs look visually similar but follow different hierarchy rules depending on the relationship between the two elements.

### Pattern A — Hierarchical Pair (label → value)

One element names the other. The label exists to identify what the value means.

**Visual relationship:** The label recedes. The value stands out.

**Examples:** Stat card ("Credit Score" / "712"), info bar ("Report:" / "credit-report.pdf"), form field ("Monthly Income" / "$4,200").

**Typography rules:**

| Element | Token | Rationale |
|---------|-------|-----------|
| Label | `labelSm` or `label` | Medium weight, smaller or equal size |
| Value (populated) | `bodyMd`, `bodySm`, `headingSm`, or KPI scale | Must be visually dominant |
| Value (empty) | Same token as populated value | Empty does not mean absent importance |

**Token usage:**

| Element | Text token |
|---------|-----------|
| Label | `text.tertiary` |
| Value — metadata context | `text.secondary` |
| Value — KPI context | `text.primary` |
| Value — interactive | `text.link` + hover: `text.linkHover` |
| Value — empty state | Same token as populated state |

**Hierarchy rule (non-negotiable):** The value must always be at a higher or equal contrast tier than the label. A label and its value must never share the same text token.

### Pattern B — Parallel Pair (primary → secondary)

Two elements of similar conceptual weight exist alongside each other. Neither is purely a label for the other.

**Visual relationship:** Both elements are visible. One is primary, one is supporting.

**Examples:** Budget header ("Housing" / "$1,450/mo"), table row ("Chase Sapphire" / "$8,400"), lead list ("John Smith" / "New York, NY").

**Typography rules:**

| Element | Token |
|---------|-------|
| Primary | `bodyMd` or `headingSm` |
| Secondary | `bodyMd` or `bodySm` |

**Token usage:**

| Element | Text token |
|---------|-----------|
| Primary | `text.primary` or `text.secondary` |
| Secondary | `text.secondary` or `text.tertiary` |

**Hierarchy rule:** The primary element must be at least as prominent as the secondary. Both may be the same tier when they carry equal weight.

### Detection rule

> "Could I remove the first element and the second element would still make sense on its own?"

| Answer | Pattern |
|--------|---------|
| No — without the label, the value has no meaning | Hierarchical Pair (A) |
| Yes — both elements carry independent meaning | Parallel Pair (B) |

**Secondary signals:**

| Signal | Likely pattern |
|--------|---------------|
| First element ends in a colon | Hierarchical Pair |
| First element is a category or entity name | Parallel Pair |
| Second element is the primary user need | Hierarchical Pair |
| Second element is a supporting fact | Parallel Pair |
| Pair appears in a stat card or KPI display | Hierarchical Pair |
| Pair appears in a table row or list item | Parallel Pair |

When ambiguous, default to Hierarchical Pair and apply the stricter hierarchy rule.

### When NOT to apply label/value hierarchy rules

- **Parallel Pair components** — table rows, list items, category headers
- **Form inputs** — input value governed by input styling, not hierarchy
- **Inline label+value on one line** — label may use `text.secondary` instead of `text.tertiary`
- **Sub-category headers** — these are Parallel Pair primary elements
- **Column headers** — special case, header = `text.tertiary`, cells = `text.secondary`+
- **Empty state headings** — not a label/value pair at all

---

## 12. Token Reference — Brand (Tier 1)

Raw primitive values. **Never reference directly in components.**

### Colors

**Blue:** 50 `#EFF6FF`, 100 `#DBEAFE`, 200 `#BFDBFE`, 300 `#93C5FD`, 400 `#60A5FA`, 500 `#3B82F6`, 600 `#2563EB`, 700 `#1D4ED8`, 800 `#1E40AF`, 900 `#1E3A8A`

**Neutral:** 0 `#FFFFFF`, 50 `#F9FAFB`, 100 `#F3F4F6`, 200 `#E5E7EB`, 300 `#D1D5DB`, 400 `#9CA3AF`, 500 `#6B7280`, 600 `#4B5563`, 700 `#374151`, 800 `#1F2937`, 900 `#111827`

**Green:** 50 `#F0FDF4`, 100 `#DCFCE7`, 200 `#BBF7D0`, 500 `#22C55E`, 600 `#16A34A`, 700 `#15803D`, 900 `#14532D`

**Red:** 50 `#FEF2F2`, 100 `#FEE2E2`, 200 `#FECACA`, 500 `#EF4444`, 600 `#DC2626`, 700 `#B91C1C`, 900 `#7F1D1D`

**Yellow:** 50 `#FEFCE8`, 100 `#FEF9C3`, 200 `#FEF08A`, 500 `#EAB308`, 600 `#CA8A04`, 700 `#A16207`, 900 `#713F12`

**Orange:** 50 `#FFF7ED`, 100 `#FFEDD5`, 500 `#F97316`, 600 `#EA580C`, 700 `#C2410C`

### Font

- Sans: `Inter, 'Helvetica Neue', Arial, sans-serif`
- Mono: `'JetBrains Mono', 'Fira Code', 'Courier New', monospace`
- Weights: regular (400), medium (500), semibold (600), bold (700)

### Radius

none (0px), sm (4px), md (6px), lg (8px), xl (12px), full (9999px)

### Shadow

- sm: `0 1px 2px 0 rgba(0,0,0,0.05)`
- md: `0 4px 6px -1px rgba(0,0,0,0.1), 0 2px 4px -2px rgba(0,0,0,0.1)`
- lg: `0 10px 15px -3px rgba(0,0,0,0.1), 0 4px 6px -4px rgba(0,0,0,0.1)`
- xl: `0 20px 25px -5px rgba(0,0,0,0.1), 0 8px 10px -6px rgba(0,0,0,0.1)`

### Duration & Easing

- instant (0ms), fast (100ms), normal (200ms), slow (350ms)
- standard: `cubic-bezier(0.4, 0, 0.2, 1)`, decelerate: `cubic-bezier(0, 0, 0.2, 1)`, accelerate: `cubic-bezier(0.4, 0, 1, 1)`

---

## 13. Token Reference — Alias (Tier 2)

Semantic tokens referencing brand tokens. Use these when no mapped token exists.

### Color Aliases

| Group | Token | References |
|-------|-------|-----------|
| Primary | `color.primary.default` | `blue.600` |
| Primary | `color.primary.hover` | `blue.700` |
| Primary | `color.primary.active` | `blue.800` |
| Primary | `color.primary.subtle` | `blue.50` |
| Primary | `color.primary.muted` | `blue.100` |
| Primary | `color.primary.text` | `blue.700` |
| Success | `color.success.default` | `green.600` |
| Success | `color.success.hover` | `green.700` |
| Success | `color.success.subtle` | `green.50` |
| Success | `color.success.muted` | `green.100` |
| Danger | `color.danger.default` | `red.600` |
| Danger | `color.danger.hover` | `red.700` |
| Danger | `color.danger.subtle` | `red.50` |
| Danger | `color.danger.muted` | `red.100` |
| Warning | `color.warning.default` | `yellow.600` |
| Warning | `color.warning.hover` | `yellow.700` |
| Warning | `color.warning.subtle` | `yellow.50` |
| Warning | `color.warning.muted` | `yellow.100` |
| Info | `color.info.default` | `blue.500` |
| Info | `color.info.subtle` | `blue.50` |
| Info | `color.info.muted` | `blue.100` |

### Text Aliases

| Token | References | Value |
|-------|-----------|-------|
| `text.primary` | `neutral.900` | `#111827` |
| `text.secondary` | `neutral.600` | `#4B5563` |
| `text.tertiary` | `neutral.500` | `#6B7280` |
| `text.placeholder` | `neutral.400` | `#9CA3AF` |
| `text.disabled` | `neutral.300` | `#D1D5DB` |
| `text.inverse` | `neutral.0` | `#FFFFFF` |
| `text.link` | `blue.600` | `#2563EB` |
| `text.linkHover` | `blue.700` | `#1D4ED8` |

### Background Aliases

| Token | References | Value |
|-------|-----------|-------|
| `background.page` | `neutral.50` | `#F9FAFB` |
| `background.surface` | `neutral.0` | `#FFFFFF` |
| `background.raised` | `neutral.0` | `#FFFFFF` |
| `background.subtle` | `neutral.50` | `#F9FAFB` |
| `background.muted` | `neutral.100` | `#F3F4F6` |
| `background.inverse` | `neutral.900` | `#111827` |

### Border Aliases

| Token | References | Value |
|-------|-----------|-------|
| `border.default` | `neutral.200` | `#E5E7EB` |
| `border.strong` | `neutral.300` | `#D1D5DB` |
| `border.focus` | `blue.500` | `#3B82F6` |
| `border.error` | `red.500` | `#EF4444` |
| `border.success` | `green.500` | `#22C55E` |

---

## 14. Token Reference — Mapped (Tier 3)

Component-specific tokens. **These are what engineers consume directly in code.**

### Button

| Variant | Token | References |
|---------|-------|-----------|
| Primary | `button.primary.bg` | `color.primary.default` → `#2563EB` |
| Primary | `button.primary.bgHover` | `color.primary.hover` → `#1D4ED8` |
| Primary | `button.primary.bgActive` | `color.primary.active` → `#1E40AF` |
| Primary | `button.primary.bgDisabled` | `neutral.200` → `#E5E7EB` |
| Primary | `button.primary.text` | `neutral.0` → `#FFFFFF` |
| Primary | `button.primary.textDisabled` | `neutral.400` → `#9CA3AF` |
| Secondary | `button.secondary.bg` | `neutral.0` → `#FFFFFF` |
| Secondary | `button.secondary.bgHover` | `neutral.50` → `#F9FAFB` |
| Secondary | `button.secondary.border` | `neutral.300` → `#D1D5DB` |
| Secondary | `button.secondary.text` | `neutral.800` → `#1F2937` |
| Danger | `button.danger.bg` | `color.danger.default` → `#DC2626` |
| Danger | `button.danger.bgHover` | `color.danger.hover` → `#B91C1C` |
| Danger | `button.danger.text` | `neutral.0` → `#FFFFFF` |
| Ghost | `button.ghost.bg` | `transparent` |
| Ghost | `button.ghost.bgHover` | `neutral.100` → `#F3F4F6` |
| Ghost | `button.ghost.text` | `neutral.700` → `#374151` |

**Sizes:** sm (28px, paddingX 12px, font 12px), md (36px, paddingX 16px, font 14px), lg (44px, paddingX 20px, font 16px). All use `radius.md` (6px).

### Input

| Token | Value |
|-------|-------|
| `input.bg` | `#FFFFFF` |
| `input.bgDisabled` | `#F9FAFB` |
| `input.border` | `#D1D5DB` |
| `input.borderHover` | `#9CA3AF` |
| `input.borderFocus` | `#3B82F6` |
| `input.borderError` | `#EF4444` |
| `input.borderDisabled` | `#E5E7EB` |
| `input.text` | `#111827` |
| `input.textPlaceholder` | `#9CA3AF` |
| `input.label` | `#374151` |
| `input.helperText` | `#6B7280` |
| `input.errorText` | `#DC2626` |
| `input.radius` | 6px |
| `input.paddingX` | 12px |
| `input.paddingY` | 8px |
| `input.height` | 36px |

### Badge

| Variant | bg | text |
|---------|-----|------|
| Neutral | `neutral.100` `#F3F4F6` | `neutral.700` `#374151` |
| Primary | `blue.100` `#DBEAFE` | `blue.700` `#1D4ED8` |
| Success | `green.100` `#DCFCE7` | `green.700` `#15803D` |
| Danger | `red.100` `#FEE2E2` | `red.700` `#B91C1C` |
| Warning | `yellow.100` `#FEF9C3` | `yellow.700` `#A16207` |

Radius: full (9999px). PaddingX: 8px. PaddingY: 2px. Font: 12px.

### Card

bg: `#FFFFFF`, border: `#E5E7EB`, shadow: sm, radius: 8px, padding: 24px.

### Table

headerBg: `#F9FAFB`, headerText: `#4B5563`, rowBg: `#FFFFFF`, rowBgHover: `#F9FAFB`, rowBgSelected: `#EFF6FF`, rowBorder: `#F3F4F6`, cellText: `#1F2937`, cellPaddingX: 16px, cellPaddingY: 12px.

### Modal

bg: `#FFFFFF`, border: `#E5E7EB`, shadow: xl, radius: 12px, overlay: `rgba(0,0,0,0.4)`, padding: 24px.

### Sidebar

bg: `#FFFFFF`, border: `#E5E7EB`, itemText: `#374151`, itemBgHover: `#F3F4F6`, itemBgActive: `#EFF6FF`, itemTextActive: `#1D4ED8`, width: 240px.

### Toast

| Variant | bg | border | text |
|---------|-----|--------|------|
| Success | `green.50` | `green.600` | `green.700` |
| Danger | `red.50` | `red.600` | `red.700` |
| Warning | `yellow.50` | `yellow.600` | `yellow.700` |
| Info | `blue.50` | `blue.500` | `blue.700` |

Radius: 8px. Shadow: lg. Padding: 16px.

### Avatar

Sizes: sm (24px), md (32px), lg (40px), xl (56px). bg: `neutral.200`, text: `neutral.700`, radius: full.

---

## 15. Token Reference — Spacing

See [Section 6 — Spacing](#6-spacing) for the complete scale, layout defaults, and component spacing tokens.

---

## 16. Token Reference — Typography

See [Section 5 — Typography](#5-typography) for the complete type scale.

### Usage Mappings

| Context | Token |
|---------|-------|
| Page title (H1) | `displayMd` |
| Section title (H2) | `headingLg` |
| Card/panel title | `headingMd` |
| Form label | `label` |
| Input text | `bodyMd` |
| Helper/error text | `bodyXs` |
| Table column header | `labelSm` |
| Table cell | `bodyMd` |
| Button text | `label` |
| Badge text | `bodyXs` |
| Nav item text | `bodyMd` |
| Meta/timestamps | `bodySm` |

---

## 17. Claude Code Operational Rules

### Non-negotiable rules

**Token usage:**
1. Never hardcode hex values in any component code. Always reference a token.
2. Never reference brand tokens directly in component code. Use mapped or alias tokens.
3. Always check mapped tokens first. If a mapped token exists for the slot, use it.
4. If no mapped token exists, use an alias token.
5. If no alias token covers the case, flag it — do not guess.

**Component generation:**
6. Check section 7 (Components) before generating any component.
7. Every interactive component must include all states: default, hover, focus, active, disabled, loading (if async), error (if applicable).
8. Focus rings must always be visible. Use `outline: 2px solid` with `border.focus` token.
9. Every form input must have a visible label above it — never placeholder-as-label.
10. Every table or list must have a defined empty state.

**Patterns:**
11. One primary action per view. Primary buttons are never paired — use primary + secondary.
12. Destructive actions always use the `danger` variant and require a confirmation dialog.
13. Toasts go bottom-right, auto-dismiss after 4–6 seconds, persist for errors.
14. Forms use `space.4` (16px) vertical row gap and `space.4` horizontal column gap.

**Typography:**
15. Default body text is `bodyMd` (14px / 400 / Inter).
16. Form labels use `label` (14px / 500 / Inter) — not `bodyMd`.
17. Use `mono` font for IDs, reference numbers, and numeric codes.
18. Never go below 12px (`bodyXs`) for any actionable text element.

**Layout:**
19. Page background: `background.page` (`#F9FAFB`). Cards/surfaces: `background.surface` (`#FFFFFF`).
20. Sidebar width: 240px. Top bar height: 56px. Page horizontal margin: 32px.
21. Spacing must always be a multiple of 4 for layout and page-level tokens. Exception: `component.labelGap = 6px` is intentional.

### How to reference tokens in code

```css
/* Button primary — correct */
background-color: var(--button-primary-bg);      /* resolves to #2563EB */
color: var(--button-primary-text);               /* resolves to #FFFFFF */
border-radius: var(--button-radius);             /* resolves to 6px */

/* Input — correct */
border: 1px solid var(--input-border);           /* resolves to #D1D5DB */
border-color on focus: var(--input-border-focus); /* resolves to #3B82F6 */
```

### When generating a component

1. Read the relevant section in this document.
2. Identify the component type — button, input, table, card, modal, badge, toast.
3. List all required states before writing any code.
4. Map every visual property to a token (background, border, text, radius, shadow, padding).
5. Apply typography using the type scale.
6. Apply spacing using the spacing scale.
7. Output the component with inline comments showing which token each value comes from.
8. Call out any gaps where no token exists — do not silently use arbitrary values.

### What Claude should NOT do

- Do not introduce new component patterns not in this document without flagging it.
- Do not use hardcoded colors or spacing values.
- Do not remove or simplify focus states for "cleaner" code.
- Do not add extra features, animations, or behaviors not asked for.
- Do not use color alone to communicate state — always pair with text or icon.
- Do not skip states (hover, disabled, error) in generated components.
- Do not use placeholder text as a substitute for a visible label.

---

## 18. Quick Reference — Key Tokens

### Colors (most used)

| What | Token | Value |
|------|-------|-------|
| Page background | `background.page` | `#F9FAFB` |
| Surface (card/modal) | `background.surface` | `#FFFFFF` |
| Default border | `border.default` | `#E5E7EB` |
| Primary button bg | `button.primary.bg` | `#2563EB` |
| Input border | `input.border` | `#D1D5DB` |
| Focus ring | `border.focus` | `#3B82F6` |
| Error border | `input.borderError` | `#EF4444` |
| Primary text | `text.primary` | `#111827` |
| Secondary text | `text.secondary` | `#4B5563` |
| Placeholder | `text.placeholder` | `#9CA3AF` |

### Typography (most used)

| What | Token | Size / Weight |
|------|-------|--------------|
| Default UI text | `bodyMd` | 14px / 400 |
| Form label | `label` | 14px / 500 |
| Table column header | `labelSm` | 12px / 500 |
| Badge text | `bodyXs` | 12px / 400 |
| Card title | `headingMd` | 20px / 600 |
| Page title | `displayMd` | 28px / 700 |
| IDs / codes | `mono` | 13px / 400 |

### Spacing (most used)

| Token | Value | Use |
|-------|-------|-----|
| `space.1` | 4px | Icon gap |
| `space.2` | 8px | Inline gap |
| `space.3` | 12px | Input padding |
| `space.4` | 16px | Form row gap, standard padding |
| `space.6` | 24px | Card padding |
| `space.8` | 32px | Section break |

### Layout

| What | Value |
|------|-------|
| Sidebar width | 240px |
| Top bar height | 56px |
| Page margin (desktop) | 32px |
| Card border radius | 8px (`radius.lg`) |
| Input border radius | 6px (`radius.md`) |
| Button border radius | 6px (`radius.md`) |
