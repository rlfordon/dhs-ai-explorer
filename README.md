# DHS AI Use Case Inventory Explorer

Interactive explorer for the Department of Homeland Security's AI Use Case Inventory — 236 AI systems, searchable and filterable, with curated annotations from watchdog organizations and class taxonomy analysis.

**Live site:** [rlfordon.github.io/dhs-ai-explorer](https://rlfordon.github.io/dhs-ai-explorer/)

## Features

- **236 use cases** from the DHS AI inventory (January 2026)
- **Filters** by agency, topic, impact level, appeal process, PII usage, and free-text search
- **Flagged systems** — 17 annotated with class taxonomy notes and/or watchdog findings from the Brennan Center, TechPolicy.Press, EFF, American Immigration Council, and GAO
- **Stats dashboard** with key numbers and systemwide findings
- **Color-coded badges** for impact level, appeal status, demographic variable usage, and PII

## Context

Built for Class 9-1: Algorithmic Bias & Discrimination. The annotations highlight systems that illustrate concepts from the course's bias taxonomy (historical bias, measurement bias, deployment bias, automation bias, representation bias) and flag concerns raised by external watchdog organizations.

## Data Source

The underlying data comes from the official [DHS AI Use Case Inventory](https://www.dhs.gov/publication/ai-use-case-inventory-library) — specifically the **2025 DHS AI Use Case Inventory** (XLSX, 162.31 KB, published 01/28/2026).

- `dhs-ai-data.json` — 236 use cases extracted from that spreadsheet
- `dhs-ai-annotations.json` — Curated annotations (editable independently of the app)

To update annotations, edit `dhs-ai-annotations.json` only. No code changes needed.

## Deployment

Pure static HTML/CSS/JS — no build step, no backend, no API keys. Works on GitHub Pages, Render, or any static host.

## License

[MIT](LICENSE)
