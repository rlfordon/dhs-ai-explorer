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

## Watchdog Sources & Further Reading

The annotations in this explorer draw from published analyses by these organizations:

- **Brennan Center for Justice** — [A Start on AI Transparency at DHS, but Room to Grow](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow) — Detailed review of inventory gaps: only 19% of projects reported complete training data documentation, 95% contracted to vendors, and risk mitigations often "indecipherable to an outsider."
- **TechPolicy.Press** — [Five Findings from an Analysis of the U.S. Department of Homeland Security's AI Inventory](https://www.techpolicy.press/five-findings-from-an-analysis-of-the-us-department-of-homeland-securitys-ai-inventory/) — Found DHS self-certified compliance, left inventory fields blank, and does not notify people or attorneys when AI is used in their case.
- **140+ Organizations Coalition (via EFF)** — [EFF and 140+ Other Organizations Call to End AI Use in Immigration Decisions](https://www.eff.org/deeplinks/2024/09/eff-140-other-organizations-call-end-ai-use-immigration-decisions) — Called on DHS to suspend non-compliant AI tools used for life-impacting immigration decisions.
- **American Immigration Council** — [Invisible Gatekeepers: DHS's Growing Use of AI in Immigration Decisions](https://www.americanimmigrationcouncil.org/blog/invisible-gatekeepers-dhs-growing-use-of-ai-in-immigration-decisions/) — Found 27 of 105 immigration use cases labeled "rights-impacting" and 16 involving facial recognition.
- **GAO** — [DHS AI for Cybersecurity Audit](https://www.gao.gov/products/gao-24-106246) — Audited DHS AI use for cybersecurity, found key responsible AI practices not fully implemented.
- **FedScoop / Nextgov** — [DHS AI Inventory: Mobile Fortify & Palantir](https://fedscoop.com/dhs-ai-inventory-mobile-fortify-palantir/) — Reported that Mobile Fortify is actively deployed in immigration enforcement while its AI impact assessment remains incomplete.

## Deployment

Pure static HTML/CSS/JS — no build step, no backend, no API keys. Works on GitHub Pages, Render, or any static host.

## License

[MIT](LICENSE)
