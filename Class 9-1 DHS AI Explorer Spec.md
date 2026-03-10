# DHS AI Inventory Explorer — Spec

## Purpose
Let students explore DHS's 236 AI use cases through a searchable, filterable interface with curated annotations highlighting what watchdog organizations and our class taxonomy have flagged. Better than a raw spreadsheet; not as heavy as a full guided tutorial.

## Target
- Vibe-code in Lovable, Replit, or similar
- No backend, no API keys
- Data baked into the app as JSON (extracted from the Excel file)
- Single page, responsive

## Data Files (already extracted)

Two JSON files in `Scratch/`, ready to drop into the app:

### `Scratch/dhs-ai-data.json` — Main inventory data
236 use cases, each with these fields:

| Field | Source Column |
|-------|-------------|
| `id` | Use Case ID |
| `name` | Use Case Name |
| `agency` | Bureau/Component |
| `status` | Stage of Development (normalized: Deployed / Pre-deployment / Pilot / Retired) |
| `impactLevel` | Is the AI use case high-impact? (normalized: High-impact / Presumed high-impact / Not high-impact) |
| `topic` | Use Case Topic Area |
| `aiType` | AI Classification |
| `problemStatement` | What problem is the AI intended to solve? |
| `expectedBenefits` | What are the expected benefits...? |
| `outputs` | Describe the AI system's outputs |
| `vendor` | Vendor(s) Name |
| `trainingData` | Describe any data used to train... |
| `usesPII` | Does this AI use case involve PII? (normalized: Yes / No) |
| `demographicVariables` | Which demographic variables does the AI use? |
| `riskAssessment` | What are the potential impacts...? |
| `appealProcess` | Is there an established appeal process? (normalized: Yes / Not applicable / In-progress / Law precludes / blank) |

### `Scratch/dhs-ai-annotations.json` — Annotations (separate, easy to edit)
Structure:
- `stats` — summary numbers for the dashboard (totalUseCases, noAppealProcess, noAppealHighImpact, etc.)
- `annotations` — keyed by Use Case ID. Each entry has:
  - `classNote` (string or null) — professor's note connecting to bias taxonomy
  - `watchdog` (array) — each with `source`, `concern`, and `url`
- `systemwideFindings` — array of findings that apply across the whole inventory, each with `source`, `finding`, `url`

17 systems annotated (7 with class notes, 10+ with watchdog flags, some with both). 5 systemwide findings.

**To update annotations later**: edit `dhs-ai-annotations.json` only. No app code changes needed.

## Layout

### Header
- Title: "DHS AI Use Case Inventory Explorer"
- Subtitle: "236 AI systems used by the Department of Homeland Security — Class 9-1: Algorithmic Bias & Discrimination"
- Source attribution: "Data: DHS AI Use Case Inventory (January 2026)"

### Filters Bar (horizontal, sticky)
- **Agency**: Dropdown/multi-select (CBP, ICE, TSA, USCIS, USSS, FEMA, CISA, MGMT, DHS Enterprise)
- **Topic**: Dropdown (Law Enforcement, Transportation, Administrative Functions, Cybersecurity, etc.)
- **Impact Level**: Toggle (High-impact / Presumed high-impact / Not high-impact)
- **Appeal Process**: Dropdown (Yes / Not applicable / In-progress / Law precludes / Blank)
- **Uses PII**: Toggle (Yes / No)
- **Search**: Free text search across Name, Problem Statement, Outputs, Training Data
- **Show flagged only**: Toggle to filter to annotated/flagged use cases
- Clear all filters button
- Result count: "Showing X of 236 use cases"

### Main View: Card List
Each use case displayed as an expandable card:

**Collapsed view (one line per card):**
- ID | Agency badge | Name | Topic | Impact badge | Appeal status badge

**Color-coded badges:**
- Impact: Red for high-impact, orange for presumed, gray for not
- Appeal: Red for "law precludes" or blank, yellow for "in-progress," green for "yes"
- Demographic variables: Red badge if any demographics are used

**Expanded view (click to expand):**
- Problem Statement (full text)
- Outputs (full text)
- Training Data (full text)
- Demographic Variables
- Risk Assessment (DHS's own words)
- Appeal Process (full text)
- Vendor

### Flagged Use Cases Panel
A right sidebar or top banner that shows curated annotations. When a use case has been flagged, show a colored banner at the top of its expanded card:

**Annotation sources:**

1. **Class taxonomy flags** (from our bias taxonomy — tagged by professor):
   - Hurricane Score (DHS-2408): "Illustrates historical bias, measurement bias, deployment bias, and automation bias simultaneously. Ask: What does 'absconding' actually measure?"
   - ICE Resume Screening (DHS-2556): "Uses GPT-4 to reduce hiring bias — but is an off-the-shelf LLM the right tool? Deployment bias."
   - USCIS Text Analytics (DHS-130): "Scans immigration narratives for 'suspicious patterns.' What makes a pattern suspicious? Measurement bias."
   - ICE Tip Processing (DHS-2515): "Summarizes and categorizes tips — but categorization IS prioritization. Deployment bias."
   - SmartLINK Biometric Check-in (DHS-407): "Facial recognition for check-ins. If it fails, you get flagged. Representation bias."
   - ICE Lead Identification (DHS-2578): "LLM extracts addresses from rap sheets. Wrong address = wrong door."
   - Facial Recognition for Vulnerable Populations (DHS-2457): "Same tech used for enforcement and protection. Same tool, different framing."

2. **Watchdog flags** (from published analyses — include source name, link, and specific concern):

   **Hurricane Score (DHS-2408)**:
   - Brennan Center: "Appropriately designated rights-impacting," but inventory notes risks of "incorrectly giving high scores" and "unrepresentative training data" with inadequate mitigation details. [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)
   - TechPolicy.Press: Used for detention decisions "without notifying impacted individuals or attorneys of AI involvement." [Source](https://www.techpolicy.press/five-findings-from-an-analysis-of-the-us-department-of-homeland-securitys-ai-inventory/)
   - 140+ Orgs Coalition: Specifically named in letter calling on DHS to suspend non-compliant AI tools by Dec 1, 2024. Estimates detainee's flight risk — coalition argues it violates federal responsible AI requirements for life-impacting decisions. [Source](https://www.eff.org/deeplinks/2024/09/eff-140-other-organizations-call-end-ai-use-immigration-decisions)

   **CBP One App / CBP Home**:
   - Brennan Center: Investigated by DHS Office for Civil Rights. "Functions in only three languages, requires a smartphone, frequently glitches," and "fails to recognize darker-skinned migrants." [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)
   - TechPolicy.Press: Uses facial recognition and data tracking "without permission or knowledge." [Source](https://www.techpolicy.press/five-findings-from-an-analysis-of-the-us-department-of-homeland-securitys-ai-inventory/)

   **Asylum Text Analytics (ATA)** (now shut down):
   - TechPolicy.Press: "Lends itself to erroneous denials, bias, and discrimination against limited English proficiency speakers, a large majority of asylum applicants." Shut down by end of 2024. [Source](https://www.techpolicy.press/five-findings-from-an-analysis-of-the-us-department-of-homeland-securitys-ai-inventory/)
   - 140+ Orgs Coalition: Named as tool that screens and flags asylum applications as fraudulent. [Source](https://www.eff.org/deeplinks/2024/09/eff-140-other-organizations-call-end-ai-use-immigration-decisions)

   **Babel (CBP social media monitoring)**:
   - Brennan Center: "Searches and aggregates social media data" to screen travelers. Raises surveillance and discrimination concerns. [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)
   - TechPolicy.Press: DHS sought extensions for this social media monitoring program "despite civil rights concerns." [Source](https://www.techpolicy.press/five-findings-from-an-analysis-of-the-us-department-of-homeland-securitys-ai-inventory/)

   **USCIS ARGOS (Fraud Risk Analysis) (DHS-181)**:
   - Brennan Center: Inventory lists known risks including "data collection bias, lack of domain-specific accuracy, limited generalization to unseen data" — but risk mitigation explanations are "indecipherable to an outsider." [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)

   **Mobile Fortify (DHS-2731 CBP / DHS-2577 ICE)**:
   - Nextgov/FedScoop: Listed as high-impact and actively deployed in immigration enforcement, but ICE still working on completing an AI impact assessment. Potential impacts "have yet to be identified" — despite active deployment. [Source](https://fedscoop.com/dhs-ai-inventory-mobile-fortify-palantir/)

   **ICE Real-Time Translation (DHS-2749)**:
   - Brennan Center: Despite DHS determination that it doesn't impact rights, the tool likely communicates "rights to migrants, agency expectations, and critical deadlines that, if missed, may waive a person's rights." [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)

   **Fivecast ONYX (social media risk assessment)**:
   - Brennan Center: Uses "vast amounts of publicly and commercially available data" for risk assessments. Needs "testing, proof of efficacy, additional transparency, and oversight." [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)
   - TechPolicy.Press: DHS requested extensions despite potential bias issues. [Source](https://www.techpolicy.press/five-findings-from-an-analysis-of-the-us-department-of-homeland-securitys-ai-inventory/)

   **Autonomous CBP Border Detection**:
   - Brennan Center: Primary purpose is to "locate, identify, interdict, and detain people" — yet DHS designated it as non-rights-impacting. [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)

   **Passenger Targeting and Vetting (related to DHS-2389)**:
   - Brennan Center: Integrates AI outputs into the Automated Targeting System (ATS), "creating compounded civil rights risks through integration with problematic automated systems." [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)

   **Systemwide findings** (not tied to a single use case):
   - TechPolicy.Press: DHS failed to fill in all inventory fields, self-certified compliance instead of using third parties, does not notify people or attorneys when AI is used in their case. [Source](https://www.techpolicy.press/five-findings-from-an-analysis-of-the-us-department-of-homeland-securitys-ai-inventory/)
   - Brennan Center: Only 19% of projects reported complete documentation about training data and trustworthiness. Only 5% developed entirely in-house; 95% contracted out. Only 16% of contracted cases reported performance metrics. [Source](https://www.brennancenter.org/our-work/analysis-opinion/start-ai-transparency-dhs-room-grow)
   - American Immigration Council: 27 of 105 immigration use cases labeled "rights-impacting." 16 involve facial recognition or face capture. [Source](https://www.americanimmigrationcouncil.org/blog/invisible-gatekeepers-dhs-growing-use-of-ai-in-immigration-decisions/)
   - GAO: Audited DHS AI for cybersecurity, found key practices not fully implemented. [Source](https://www.gao.gov/products/gao-24-106246)

### Stats Dashboard (top of page, collapsible)
Quick summary numbers (from `annotations.json` → `stats`):
- 236 total use cases
- 221 with no appeal process (the big number)
- 89 of 104 high-impact/presumed with no appeal
- Only 10 systems with an established appeal process
- 6 systems explicitly using demographic variables
- Agency breakdown: CBP (83), ICE (50), TSA (26), USCIS (23), FEMA (22), CISA (7), MGMT (10), others (15)
- Topic breakdown (mini bar chart or counts)

## Interaction Details

### Expandable cards
- Click card to expand/collapse
- Only one card expanded at a time (accordion) OR allow multiple (test both)
- Expanded card shows all fields with clear labels

### Annotation display
- Flagged cards show a small icon/badge in collapsed view
- Expanded view shows the annotation text in a highlighted box
- Two annotation types visually distinct: "Class Note" (scarlet, from taxonomy) vs "Watchdog Flag" (blue, from external analysis)

### Search behavior
- Real-time filtering as you type
- Searches across: Name, Problem Statement, Outputs, Training Data
- Highlight matching terms in expanded view

## Visual Design
- Clean, minimal — similar to Document Tech Gallery aesthetic
- OSU scarlet (#ba0c2f) for accent/branding
- Light gray cards on white background
- Readable at laptop screen size (students will use in class)
- Mobile-friendly but laptop is primary

## Data Extraction
Already done. Both JSON files are in `Scratch/` and ready to import into the app. If the source Excel is updated, re-run the extraction script from the session that created these files.

## What This Is NOT
- Not a full analysis tool — students don't need to sort by every column or export data
- Not an argument — the annotations flag what's interesting, but students draw their own conclusions
- Not comprehensive watchdog coverage — just enough to show that external organizations are paying attention to this data

## Build Priority
1. ~~Data extraction + JSON~~ ✓ Done — `Scratch/dhs-ai-data.json` + `Scratch/dhs-ai-annotations.json`
2. Card list with expand/collapse
3. Filters (agency, topic, appeal, search)
4. Flagged annotations (read from annotations JSON)
5. Stats dashboard
6. Visual polish

## Decisions
- **Hosting**: Render (like Doc Tech Gallery)
- **Bias taxonomy**: Not in the app — keep that in lecture/Canvas
- **Discussion questions**: Canvas page only, not in the app
