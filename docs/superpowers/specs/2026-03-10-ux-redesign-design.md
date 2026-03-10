# UX Redesign: Sidebar Filters + Simplified Cards

**Date:** 2026-03-10
**Status:** Approved

## Context

The DHS AI Explorer is an interactive single-page tool for a university class on algorithmic bias. It displays 236 DHS AI use cases with filtering and annotations. The current UI has a horizontal filter bar with 8+ filters that feels crowded, and cards show up to 6 color-coded badges that create visual clutter without helping students decide which cards to explore.

## Design Decisions

### 1. Sidebar Filters

Move all filters from the horizontal top bar into a left sidebar (~250px wide).

**Sidebar contents (top to bottom):**
- Search text input (full width, top position)
- Agency dropdown (many values)
- Topic dropdown (many values)
- Impact Level checkboxes: High-impact, Presumed high-impact, Not high-impact
- Appeal Process checkboxes: Yes, Not applicable, In-progress, Law precludes, None listed
- Status checkboxes: Deployed, Pre-deployment, Pilot, Retired
- Uses PII checkboxes: Yes, No
- Separator line
- Flagged only checkbox
- New in 2025 checkbox
- Separator line
- Result count ("Showing X of 236")
- Clear all filters link

**Behavior:**
- Sidebar is sticky (scrolls independently from card list)
- Filters apply immediately on change (no "Apply" button)
- Checkboxes allow multi-select within a group (OR logic within group, AND across groups)
- URL state syncing continues to work (checkbox selections encoded as comma-separated values)

**Mobile (<768px):**
- Sidebar hidden, replaced by a "Filters" button in a slim top bar
- Button opens sidebar as a full-screen overlay
- Overlay has a sticky "Show X results" button at bottom to close
- Simple implementation — this is primarily a laptop experience

### 2. Simplified Card Headers

Reduce badges to signal-only. Remove all category badges that duplicate sidebar filter dimensions.

**Card header shows:**
- Use Case ID (monospace)
- Agency pill (navy filled)
- Use Case Name (bold, flex: 1)
- FLAGGED badge (filled scarlet) — only on annotated cards
- NEW badge (outlined blue) — only on 2025 inventory additions
- Expand arrow

**Removed from header (moved to expanded view only):**
- Impact level badge
- Appeal process badge
- PII badge
- Demographics badge

**Rationale:** With a sidebar, students filter to find specific categories. Badges only need to answer "should I click this card?" — FLAGGED and NEW are the two signals that matter for that decision. The scarlet left-border on flagged cards provides additional visual distinction.

### 3. Two-Line Description Preview

Add a truncated description below the card header row to give students "information scent."

**Content priority for the preview text:**
1. If the card has a class note annotation → show the class note
2. If the card has watchdog concerns but no class note → show the first watchdog concern
3. Otherwise → show the DHS problem statement

**Styling:**
- Font size: smaller than card name (~0.82rem)
- Color: muted (gray-500 / slate-light)
- Truncated at 2 lines using CSS `-webkit-line-clamp: 2`
- Search highlighting still applies within the preview text

**Rationale:** The current cards are all metadata, zero content. A student sees "DHS-2408 / ICE / Risk Classification Assessment" but has no idea what the system does. The preview — especially showing the class annotation for flagged cards — gives students an immediate reason to click or skip.

### 4. Unchanged Elements

These stay as-is from the current implementation:
- Site header (navy, title, subtitle, source)
- Stats section (collapsible, default closed)
- Expanded card view (annotation banners, detail grid, all fields)
- Footer
- Back-to-top button
- Accessibility features (skip link, ARIA, keyboard nav, focus styles, contrast)
- URL state syncing for shareable filtered views
- Search debouncing
- Error handling on data load

## Data Flow Changes

- `getFiltered()` needs to support multi-select for checkbox filters (OR within group)
- URL encoding changes: `?impact=High-impact,Presumed+high-impact` instead of single value
- `renderCards()` generates simpler header HTML + preview line
- Preview text selection logic: check `ANNOTATIONS[id].classNote`, then `ANNOTATIONS[id].watchdog[0].concern`, then `d.problemStatement`

## Visual Design

- Sidebar background: white or gray-100, with right border
- Checkbox group labels: uppercase monospace (matching current filter label style)
- Sidebar inherits existing font/color system (no new colors needed)
- Card preview text: `color: var(--gray-500)`, `font-size: 0.82rem`, `line-height: 1.5`
