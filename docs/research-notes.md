# Research Notes

## DHS AI Inventory Publication Timeline

Verified 2026-03-10 from https://www.dhs.gov/publication/ai-use-case-inventory-library

DHS publishes "one main update per year and other revisions as needed." OMB issues annual guidance and DHS updates accordingly.

### Files on the DHS site

| File | Published | Use Cases |
|------|-----------|-----------|
| 2022 DHS AI Use Case Inventory | 12/13/2024 | — |
| 2023 DHS AI Use Case Inventory | 08/14/2024 | — |
| 2024 DHS AI Use Case Inventory | 12/16/2024 | ~198 (after cleanup) |
| 2024 DHS AI Use Case Inventory July Revision | 07/01/2025 | 198 |
| 2025 DHS AI Use Case Inventory | 01/28/2026 | 236 |
| 2025 DHS Common Commercial AI Inventory | 01/28/2026 | (separate file) |

### What the July 2025 revision was

The July 2025 revision was a **cleanup of the Biden-era 2024 inventory**, not Trump-era additions. Evidence:

- The file itself contains notes: *"Highlighted cells denote a change from the 12/16/2024 AI Inventory update"* and *"This update includes changes to some column header names which were mis-aligned in a previous version."*
- Of the 45 use cases dropped between the July revision and the 2025 inventory, almost all are marked "Inactive (no longer used)", "consolidated with another use case", or "research and development only"
- Examples: CBP One (retired), Asylum Text Analytics (retired), various CISA conceptual projects that were never initiated

### What's new in the 2025 inventory

The 83 new use cases (tracked as `NEW_IDS` in the code) are IDs present in the 2025 inventory (Jan 2026) but **not** in the 2024 inventory July revision (Jul 2025). These are the **Trump-era additions** — systems added to the inventory under the new administration.

One edge case: DHS-2454 "LIGER Generative AI Toolkit" appears in the July revision marked "(New)" but also carries forward into the 2025 inventory.

### 2025 format changes

Per DHS: *"The 2025 update reflects changes in format and information requirements directed by OMB in its annual guidance for 2025. Changes include streamlined reporting requirements intended to increase efficiency and understanding of use cases."*

Key change: most Common Commercial AI use cases were moved into their own separate file, so the primary inventory focuses on AI unique to DHS operations.

## Deployment Status Breakdown

From the 2025 inventory data (236 total):

| Status | Count |
|--------|-------|
| Deployed | 111 |
| Pre-deployment | 86 |
| Retired | 31 |
| Pilot | 8 |
