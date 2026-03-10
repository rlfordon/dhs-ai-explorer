# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Interactive single-page explorer for the DHS AI Use Case Inventory (236 systems). Built for a university class on algorithmic bias. Deployed on GitHub Pages at rlfordon.github.io/dhs-ai-explorer.

## Architecture

**Pure static site — no build step, no framework, no dependencies.** Everything is in `index.html` (inline CSS + JS) plus two JSON data files.

- `index.html` — The entire app: styles, markup, and all JavaScript
- `dhs-ai-data.json` — 236 use cases extracted from the DHS Excel inventory (354KB)
- `dhs-ai-annotations.json` — Curated annotations keyed by Use Case ID, plus systemwide findings and stats. **This is the file to edit when adding/updating annotations** — no code changes needed.

### Data Flow

1. `loadData()` fetches both JSON files on page load
2. `init()` builds the stats dashboard, filter controls, and card list
3. All filtering happens client-side via `getFiltered()` — filters are combined with AND logic
4. `renderCards()` regenerates the entire card list HTML on every filter change
5. `NEW_IDS` is a hardcoded Set of 83 use case IDs added since the 2024 inventory (compared against the July 2025 revision XLSX)

### Key Design Decisions

- **Annotations are separate from data** — `dhs-ai-annotations.json` can be edited independently. Structure: `annotations[useCaseId].classNote` (string) and `annotations[useCaseId].watchdog[]` (array of `{source, concern, url}`)
- **No accordion constraint** — multiple cards can be expanded simultaneously
- **Search highlights** — matching terms are wrapped in `<mark>` tags in expanded card views
- **All text is HTML-escaped** via the `esc()` function (creates a temporary DOM element)

## Development

Serve locally with any static server:
```
npx http-server -p 8765 -c-1
```

No build, lint, or test commands. Changes to `index.html` are immediately reflected on reload.

## Deployment

Push to `master` → GitHub Pages auto-deploys. No CI/CD config needed.

## Visual Design

- OSU scarlet accent: `#ba0c2f`
- Fonts: DM Serif Display (headings), Source Sans 3 (body), JetBrains Mono (IDs/labels)
- Badge color coding: red = high-impact/no appeal, orange = presumed, green = appeal exists, blue = watchdog/new
- Flagged cards have a scarlet left border

## Data Source

Official DHS AI Use Case Inventory: https://www.dhs.gov/publication/ai-use-case-inventory-library
Specific file: 2025 DHS AI Use Case Inventory (XLSX, published 01/28/2026)
