# UX Redesign Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the crowded horizontal filter bar with a sidebar, simplify card badges to FLAGGED/NEW only, and add two-line description previews with annotation-first logic.

**Architecture:** Single-file app (index.html) with inline CSS + JS. All changes go in this one file. The layout shifts from a full-width card list below a sticky horizontal filter bar to a sidebar+content flex layout. Filter state changes from single-value strings to arrays for checkbox groups (multi-select with OR logic within groups, AND across groups).

**Tech Stack:** Vanilla HTML/CSS/JS, no build step. Verify by running `npx http-server -p 8765 -c-1` and checking in browser.

**Spec:** `docs/superpowers/specs/2026-03-10-ux-redesign-design.md`

---

## Chunk 1: Layout + Sidebar

### Task 1: CSS — Replace horizontal filter bar with sidebar layout

**Files:**
- Modify: `index.html` (CSS section, lines 229–337)

This task replaces the `.filters-bar` CSS with a sidebar layout and adds all new sidebar styles.

- [ ] **Step 1: Remove old filter bar CSS**

Delete the entire `/* ─── FILTERS ─── */` CSS block (lines 229–337), including `.filters-bar`, `.filters-inner`, `.filters-row`, `.filter-group`, `.filter-group label`, `.filter-group select`, `.filter-group input`, `.filter-toggle`, `.filters-meta`, `.result-count`, `.clear-btn`, and the `.filters-bar.scrolled` rule (lines 559–563).

- [ ] **Step 2: Remove old badge CSS that's no longer needed on card headers**

Delete these CSS rules that are no longer used on card headers (they stay in expanded view via detail fields):
- `.badge-impact-high`, `.badge-impact-presumed`, `.badge-impact-not`
- `.badge-appeal-yes`, `.badge-appeal-progress`, `.badge-appeal-precludes`, `.badge-appeal-na`, `.badge-appeal-blank`
- `.badge-demo`, `.badge-pii`

Keep: `.badge` (base), `.badge-flagged`, `.badge-new`, `.card-badges`

- [ ] **Step 3: Add new layout CSS**

Replace the deleted filter CSS with the new sidebar + content layout:

```css
/* ─── APP LAYOUT ─── */
.app-layout {
  display: flex;
  max-width: 1280px;
  margin: 0 auto;
  min-height: calc(100vh - 200px);
}

/* ─── SIDEBAR ─── */
.sidebar {
  width: 260px;
  flex-shrink: 0;
  background: var(--white);
  border-right: 1px solid var(--gray-200);
  padding: 1.25rem 1.25rem 2rem;
  position: sticky;
  top: 0;
  height: 100vh;
  overflow-y: auto;
}
.sidebar-section {
  margin-bottom: 1.25rem;
}
.sidebar-section-label {
  font-family: var(--font-mono);
  font-size: 0.65rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: var(--gray-500);
  margin-bottom: 0.4rem;
}
.sidebar-search {
  width: 100%;
  font-family: var(--font-body);
  font-size: 0.82rem;
  padding: 0.5rem 0.65rem;
  border: 1px solid var(--gray-300);
  border-radius: 5px;
  background: var(--white);
  color: var(--slate);
  outline: none;
  transition: border-color 0.15s;
}
.sidebar-search:focus { border-color: var(--scarlet); }
.sidebar-select {
  width: 100%;
  font-family: var(--font-body);
  font-size: 0.82rem;
  padding: 0.4rem 0.5rem;
  border: 1px solid var(--gray-300);
  border-radius: 5px;
  background: var(--white);
  color: var(--slate);
  outline: none;
  transition: border-color 0.15s;
}
.sidebar-select:focus { border-color: var(--scarlet); }
.sidebar-checkboxes {
  display: flex;
  flex-direction: column;
  gap: 0.3rem;
}
.sidebar-checkbox {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  font-size: 0.8rem;
  color: var(--slate);
  cursor: pointer;
}
.sidebar-checkbox input[type="checkbox"] {
  accent-color: var(--scarlet);
  width: 14px;
  height: 14px;
  cursor: pointer;
}
.sidebar-divider {
  border: none;
  border-top: 1px solid var(--gray-200);
  margin: 1rem 0;
}
.sidebar-meta {
  font-size: 0.8rem;
  color: var(--gray-500);
  margin-bottom: 0.5rem;
}
.sidebar-meta strong {
  color: var(--slate);
  font-weight: 700;
}
.sidebar-clear {
  font-family: var(--font-body);
  font-size: 0.78rem;
  color: var(--scarlet);
  background: none;
  border: none;
  cursor: pointer;
  font-weight: 600;
  padding: 0;
}
.sidebar-clear:hover { text-decoration: underline; }
.sidebar-clear.hidden { display: none; }

/* ─── CONTENT AREA ─── */
.content-area {
  flex: 1;
  min-width: 0;
  padding: 1.25rem 2rem 3rem;
}

/* ─── CARD PREVIEW ─── */
.card-preview {
  font-size: 0.82rem;
  color: var(--gray-500);
  line-height: 1.5;
  margin-top: 0.35rem;
  padding: 0 1rem 0.5rem;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* ─── MOBILE FILTER BUTTON ─── */
.mobile-filter-bar {
  display: none;
  background: var(--white);
  border-bottom: 1px solid var(--gray-200);
  padding: 0.6rem 1rem;
  position: sticky;
  top: 0;
  z-index: 100;
  align-items: center;
  justify-content: space-between;
}
.mobile-filter-btn {
  font-family: var(--font-body);
  font-size: 0.85rem;
  font-weight: 600;
  padding: 0.4rem 0.8rem;
  border: 1px solid var(--gray-300);
  border-radius: 5px;
  background: var(--white);
  color: var(--slate);
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 0.4rem;
}
.mobile-filter-count {
  font-size: 0.8rem;
  color: var(--gray-500);
}
.sidebar-overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.3);
  z-index: 199;
}
.sidebar-overlay.open { display: block; }
.mobile-apply-btn {
  display: none;
  position: sticky;
  bottom: 0;
  width: 100%;
  padding: 0.75rem;
  background: var(--scarlet);
  color: var(--white);
  border: none;
  font-family: var(--font-body);
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
}
```

- [ ] **Step 4: Update responsive CSS**

Replace the existing `@media` rules (lines 544–557) with:

```css
@media (max-width: 900px) {
  .detail-grid { grid-template-columns: 1fr; }
  .stats-grid { grid-template-columns: repeat(auto-fit, minmax(140px, 1fr)); }
}
@media (max-width: 768px) {
  .sidebar {
    position: fixed;
    left: -280px;
    top: 0;
    height: 100vh;
    z-index: 200;
    transition: left 0.25s ease;
    padding-bottom: 4rem;
  }
  .sidebar.mobile-open { left: 0; }
  .sidebar.mobile-open + .sidebar-overlay { display: block; }
  .mobile-filter-bar { display: flex; }
  .mobile-apply-btn { display: block; }
  .content-area { padding: 1rem 1rem 2rem; }
  .site-header { padding: 1.5rem 1rem; }
  .card-header { padding: 0.6rem 0.75rem; }
  .card-preview { padding: 0 0.75rem 0.5rem; }
  .finding-source { min-width: 80px; }
}
```

- [ ] **Step 5: Verify CSS compiles**

Run: `npx http-server -p 8765 -c-1` and open in browser. The page will look broken (HTML hasn't changed yet) but should load without CSS parse errors. Check the browser console.

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "refactor: replace filter bar CSS with sidebar layout styles"
```

---

### Task 2: HTML — Replace filter bar with sidebar + content layout

**Files:**
- Modify: `index.html` (HTML section, lines 655–671)

- [ ] **Step 1: Replace the filters bar and main content HTML**

Replace everything from `<!-- FILTERS -->` through `</main>` (lines 655–671) with the new sidebar + content layout:

```html
<!-- MOBILE FILTER BAR (hidden on desktop) -->
<div class="mobile-filter-bar">
  <button class="mobile-filter-btn" onclick="toggleMobileSidebar()">
    &#9776; Filters
  </button>
  <span class="mobile-filter-count" id="mobileResultCount"></span>
</div>

<!-- APP LAYOUT -->
<div class="app-layout">
  <!-- SIDEBAR -->
  <aside class="sidebar" id="sidebar" role="search" aria-label="Filter use cases">
    <div id="sidebarContent"></div>
    <button class="mobile-apply-btn" onclick="toggleMobileSidebar()">
      Show <span id="mobileApplyCount">236</span> results
    </button>
  </aside>
  <div class="sidebar-overlay" id="sidebarOverlay" onclick="toggleMobileSidebar()"></div>

  <!-- MAIN CONTENT -->
  <main class="content-area">
    <div class="card-list" id="cardList">
      <div class="loading">Loading inventory&hellip;</div>
    </div>
  </main>
</div>
```

- [ ] **Step 2: Update skip link target**

The skip link `href="#cardList"` still works (the ID is preserved). No change needed.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "refactor: replace filter bar HTML with sidebar layout"
```

---

## Chunk 2: JavaScript — Filters + Cards

### Task 3: JS — Rewrite filter state and sidebar rendering

**Files:**
- Modify: `index.html` (JS section, lines 705–965)

This task changes the filter state from single-value strings to arrays for checkbox groups, rewrites `buildFilters()` to render sidebar checkboxes, updates `getFiltered()` for multi-select OR logic, and updates URL state encoding.

- [ ] **Step 1: Update filter state shape**

Replace the `filters` object and related functions. The new state uses arrays for checkbox groups:

```js
let filters = {
  agency: '',
  topic: '',
  impact: [],      // was string
  appeal: [],      // was string
  pii: [],         // was string
  status: [],      // was string
  search: '',
  flaggedOnly: false,
  newOnly: false
};
```

- [ ] **Step 2: Update URL state functions**

Replace `filtersToURL()` and `filtersFromURL()`:

```js
function filtersToURL() {
  const params = new URLSearchParams();
  if (filters.agency) params.set('agency', filters.agency);
  if (filters.topic) params.set('topic', filters.topic);
  if (filters.impact.length) params.set('impact', filters.impact.join(','));
  if (filters.appeal.length) params.set('appeal', filters.appeal.join(','));
  if (filters.pii.length) params.set('pii', filters.pii.join(','));
  if (filters.status.length) params.set('status', filters.status.join(','));
  if (filters.search) params.set('q', filters.search);
  if (filters.flaggedOnly) params.set('flagged', '1');
  if (filters.newOnly) params.set('new', '1');
  const qs = params.toString();
  history.replaceState(null, '', qs ? '?' + qs : location.pathname);
}

function filtersFromURL() {
  const params = new URLSearchParams(location.search);
  const csv = key => { const v = params.get(key); return v ? v.split(',') : []; };
  filters.agency = params.get('agency') || '';
  filters.topic = params.get('topic') || '';
  filters.impact = csv('impact');
  filters.appeal = csv('appeal');
  filters.pii = csv('pii');
  filters.status = csv('status');
  filters.search = params.get('q') || '';
  filters.flaggedOnly = params.get('flagged') === '1';
  filters.newOnly = params.get('new') === '1';
}
```

- [ ] **Step 3: Rewrite `buildFilters()` for sidebar with checkboxes**

Replace the entire `buildFilters()` function:

```js
function buildFilters() {
  const agencies = [...new Set(DATA.map(d => d.agency))].sort();
  const topics = [...new Set(DATA.map(d => d.topic).filter(Boolean))].sort();
  const el = document.getElementById('sidebarContent');

  el.innerHTML = `
    <div class="sidebar-section">
      <label class="sidebar-section-label" for="fSearch">Search</label>
      <input class="sidebar-search" type="text" id="fSearch"
             placeholder="Name, outputs, training data…"
             oninput="debouncedSearch(this.value)">
    </div>

    <div class="sidebar-section">
      <label class="sidebar-section-label" for="fAgency">Agency</label>
      <select class="sidebar-select" id="fAgency" onchange="filters.agency=this.value;renderCards()">
        <option value="">All agencies</option>
        ${agencies.map(a => `<option value="${esc(a)}">${esc(a)}</option>`).join('')}
      </select>
    </div>

    <div class="sidebar-section">
      <label class="sidebar-section-label" for="fTopic">Topic</label>
      <select class="sidebar-select" id="fTopic" onchange="filters.topic=this.value;renderCards()">
        <option value="">All topics</option>
        ${topics.map(t => `<option value="${esc(t)}">${esc(t)}</option>`).join('')}
      </select>
    </div>

    <div class="sidebar-section">
      <div class="sidebar-section-label">Impact Level</div>
      <div class="sidebar-checkboxes">
        ${['High-impact','Presumed high-impact','Not high-impact'].map(v =>
          `<label class="sidebar-checkbox">
            <input type="checkbox" value="${esc(v)}" onchange="toggleCheckbox('impact',this.value,this.checked)"> ${esc(v)}
          </label>`
        ).join('')}
      </div>
    </div>

    <div class="sidebar-section">
      <div class="sidebar-section-label">Appeal Process</div>
      <div class="sidebar-checkboxes">
        ${[
          {v:'Yes', label:'Yes'},
          {v:'Not applicable', label:'Not applicable'},
          {v:'In-progress', label:'In-progress'},
          {v:'Law precludes', label:'Law precludes'},
          {v:'__blank', label:'None listed'}
        ].map(({v,label}) =>
          `<label class="sidebar-checkbox">
            <input type="checkbox" value="${esc(v)}" onchange="toggleCheckbox('appeal',this.value,this.checked)"> ${esc(label)}
          </label>`
        ).join('')}
      </div>
    </div>

    <div class="sidebar-section">
      <div class="sidebar-section-label">Status</div>
      <div class="sidebar-checkboxes">
        ${['Deployed','Pre-deployment','Pilot','Retired'].map(v =>
          `<label class="sidebar-checkbox">
            <input type="checkbox" value="${esc(v)}" onchange="toggleCheckbox('status',this.value,this.checked)"> ${esc(v)}
          </label>`
        ).join('')}
      </div>
    </div>

    <div class="sidebar-section">
      <div class="sidebar-section-label">Uses PII</div>
      <div class="sidebar-checkboxes">
        ${['Yes','No'].map(v =>
          `<label class="sidebar-checkbox">
            <input type="checkbox" value="${esc(v)}" onchange="toggleCheckbox('pii',this.value,this.checked)"> ${esc(v)}
          </label>`
        ).join('')}
      </div>
    </div>

    <hr class="sidebar-divider">

    <div class="sidebar-section">
      <div class="sidebar-checkboxes">
        <label class="sidebar-checkbox">
          <input type="checkbox" id="fFlagged" onchange="filters.flaggedOnly=this.checked;renderCards()"> Flagged only
        </label>
        <label class="sidebar-checkbox">
          <input type="checkbox" id="fNew" onchange="filters.newOnly=this.checked;renderCards()"> New in 2025
        </label>
      </div>
    </div>

    <hr class="sidebar-divider">

    <div class="sidebar-meta" id="resultCount"></div>
    <button class="sidebar-clear" id="clearBtn" onclick="clearFilters()">Clear all filters</button>
  `;
}
```

- [ ] **Step 4: Add `toggleCheckbox()` function and update related functions**

Add after `buildFilters()`:

```js
function toggleCheckbox(group, value, checked) {
  if (checked) {
    if (!filters[group].includes(value)) filters[group].push(value);
  } else {
    filters[group] = filters[group].filter(v => v !== value);
  }
  renderCards();
}
```

Replace `toggleFlagged()`, `toggleNew()` — these are no longer needed (handled inline by checkbox `onchange`). Delete them.

Replace `hasActiveFilters()`:
```js
function hasActiveFilters() {
  return filters.agency || filters.topic || filters.impact.length || filters.appeal.length ||
    filters.pii.length || filters.status.length || filters.search || filters.flaggedOnly || filters.newOnly;
}
```

Replace `clearFilters()`:
```js
function clearFilters() {
  filters = { agency: '', topic: '', impact: [], appeal: [], pii: [], status: [], search: '', flaggedOnly: false, newOnly: false };
  applyFiltersToUI();
  renderCards();
}
```

Replace `applyFiltersToUI()`:
```js
function applyFiltersToUI() {
  const setVal = (id, val) => { const el = document.getElementById(id); if (el) el.value = val; };
  setVal('fAgency', filters.agency);
  setVal('fTopic', filters.topic);
  setVal('fSearch', filters.search);

  // Set checkboxes
  document.querySelectorAll('.sidebar-checkboxes input[type="checkbox"]').forEach(cb => {
    const group = cb.closest('.sidebar-section').querySelector('.sidebar-section-label');
    if (!group) return;
    const label = group.textContent.trim();
    const map = {'Impact Level':'impact', 'Appeal Process':'appeal', 'Status':'status', 'Uses PII':'pii'};
    const key = map[label];
    if (key) cb.checked = filters[key].includes(cb.value);
  });

  const flagged = document.getElementById('fFlagged');
  const newCb = document.getElementById('fNew');
  if (flagged) flagged.checked = filters.flaggedOnly;
  if (newCb) newCb.checked = filters.newOnly;
}
```

- [ ] **Step 5: Update `getFiltered()` for multi-select**

Replace the entire function:

```js
function getFiltered() {
  const s = filters.search.toLowerCase().trim();
  return DATA.filter(d => {
    if (filters.agency && d.agency !== filters.agency) return false;
    if (filters.topic && d.topic !== filters.topic) return false;
    if (filters.impact.length && !filters.impact.includes(d.impactLevel)) return false;
    if (filters.appeal.length) {
      const hasBlank = filters.appeal.includes('__blank');
      const others = filters.appeal.filter(v => v !== '__blank');
      const matchesOther = others.length && others.includes(d.appealProcess);
      const matchesBlank = hasBlank && d.appealProcess === '';
      if (!matchesOther && !matchesBlank) return false;
    }
    if (filters.pii.length && !filters.pii.includes(d.usesPII)) return false;
    if (filters.status.length && !filters.status.includes(d.status)) return false;
    if (filters.flaggedOnly && !ANNOTATIONS[d.id]) return false;
    if (filters.newOnly && !NEW_IDS.has(d.id)) return false;
    if (s) {
      const hay = [d.name, d.problemStatement, d.outputs, d.trainingData].join(' ').toLowerCase();
      if (!hay.includes(s)) return false;
    }
    return true;
  });
}
```

- [ ] **Step 6: Add mobile sidebar toggle**

Add function:

```js
function toggleMobileSidebar() {
  const sidebar = document.getElementById('sidebar');
  const overlay = document.getElementById('sidebarOverlay');
  sidebar.classList.toggle('mobile-open');
  overlay.classList.toggle('open');
}
```

- [ ] **Step 7: Update `setupScrollListeners()`**

Replace with (no more `.filters-bar` to track):

```js
function setupScrollListeners() {
  const backBtn = document.getElementById('backToTop');
  window.addEventListener('scroll', () => {
    backBtn.classList.toggle('visible', window.scrollY > 600);
  }, { passive: true });
}
```

- [ ] **Step 8: Update `renderCards()` result count targets**

In `renderCards()`, update the result count lines to also update the mobile count and apply button:

```js
const count = filtered.length;
document.getElementById('resultCount').innerHTML =
  `Showing <strong>${count}</strong> of <strong>236</strong> use cases`;
const mobileCount = document.getElementById('mobileResultCount');
if (mobileCount) mobileCount.textContent = `${count} of 236`;
const applyCount = document.getElementById('mobileApplyCount');
if (applyCount) applyCount.textContent = count;

const clearBtn = document.getElementById('clearBtn');
if (clearBtn) clearBtn.classList.toggle('hidden', !hasActiveFilters());
```

- [ ] **Step 9: Verify sidebar filters work**

Run: `npx http-server -p 8765 -c-1`

Test:
1. Sidebar renders with all filter groups
2. Dropdowns filter by agency/topic
3. Checkboxes multi-select (e.g., check both "High-impact" and "Presumed high-impact")
4. Flagged only and New in 2025 toggles work
5. Clear all filters resets everything
6. URL updates with comma-separated values
7. Reload with URL params restores checkbox state

- [ ] **Step 10: Commit**

```bash
git add index.html
git commit -m "feat: sidebar filters with checkboxes and multi-select"
```

---

### Task 4: JS — Simplify card headers + add preview text

**Files:**
- Modify: `index.html` (JS section — `renderCards()` and badge functions)

- [ ] **Step 1: Add `getPreviewText()` function**

Add before `renderCards()`:

```js
function getPreviewText(d) {
  const anno = ANNOTATIONS[d.id];
  if (anno && anno.classNote) return anno.classNote;
  if (anno && anno.watchdog && anno.watchdog.length > 0) return anno.watchdog[0].concern;
  return d.problemStatement || '';
}
```

- [ ] **Step 2: Simplify card header in `renderCards()`**

Replace the card template inside `renderCards()` (the `return` inside the `.map()`) with:

```js
    return `
      <div class="card ${isFlagged ? 'flagged' : ''}" id="card-${esc(d.id)}">
        <div class="card-header" role="button" tabindex="0"
             onclick="toggleCard('${esc(d.id)}')"
             onkeydown="if(event.key==='Enter'||event.key===' '){event.preventDefault();toggleCard('${esc(d.id)}')}"
             aria-expanded="false" aria-controls="body-${esc(d.id)}">
          <span class="card-id">${esc(d.id)}</span>
          <span class="card-agency">${esc(d.agency)}</span>
          <span class="card-name">${highlightSearch(esc(d.name))}</span>
          <div class="card-badges">
            ${NEW_IDS.has(d.id) ? '<span class="badge badge-new">NEW</span>' : ''}
            ${isFlagged ? '<span class="badge badge-flagged">&#9873; FLAGGED</span>' : ''}
          </div>
          <span class="card-expand-arrow" aria-hidden="true">&#9654;</span>
        </div>
        <div class="card-preview">${highlightSearch(esc(getPreviewText(d)))}</div>
        <div class="card-body" id="body-${esc(d.id)}">
          <div class="card-body-inner">
            ${hasClass ? `
              <div class="annotation-banner class-note">
                <div class="annotation-label">Class Taxonomy Note</div>
                ${esc(anno.classNote)}
              </div>
            ` : ''}
            ${hasWatchdog ? `
              <div class="annotation-banner watchdog-note">
                <div class="annotation-label">Watchdog Flags</div>
                ${anno.watchdog.map(w => `
                  <div class="watchdog-entry">
                    <div class="watchdog-source"><a href="${esc(w.url)}" target="_blank" rel="noopener">${esc(w.source)}</a></div>
                    <div>${esc(w.concern)}</div>
                  </div>
                `).join('')}
              </div>
            ` : ''}
            <div class="detail-grid">
              ${detailField('Problem Statement', d.problemStatement, true)}
              ${detailField('AI Outputs', d.outputs, true)}
              ${detailField('Training Data', d.trainingData, true)}
              ${detailField('Impact Level', d.impactLevel)}
              ${detailField('Demographic Variables', d.demographicVariables)}
              ${detailField('Risk Assessment', d.riskAssessment)}
              ${detailField('Appeal Process', d.appealProcess)}
              ${detailField('Vendor', d.vendor)}
              ${detailField('AI Type', d.aiType)}
              ${detailField('Status', d.status)}
              ${detailField('Uses PII', d.usesPII)}
              ${detailField('Expected Benefits', d.expectedBenefits, true)}
            </div>
          </div>
        </div>
      </div>
    `;
```

Note: removed `.card-topic` from header, removed impact/appeal/demo/PII badges, added `.card-preview` div, added Impact Level to the detail grid in expanded view.

- [ ] **Step 3: Delete unused functions**

Delete `impactBadge()` and `appealBadge()` — no longer called.

- [ ] **Step 4: Delete unused CSS**

Remove `.card-topic` CSS rule (line 395–399) since topic is no longer shown in card headers.

- [ ] **Step 5: Verify cards render correctly**

Run: `npx http-server -p 8765 -c-1`

Test:
1. Card headers show only: ID, agency pill, name, NEW/FLAGGED badges, arrow
2. Two-line preview appears below each card header
3. Flagged cards show class note or watchdog concern as preview
4. Non-flagged cards show problem statement as preview
5. Preview text truncates at 2 lines
6. Search highlighting works in preview text
7. Expanding a card still shows full detail grid with all fields including Impact Level
8. Mobile: sidebar opens/closes via filter button

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "feat: simplified card headers with annotation previews"
```

---

### Task 5: Final cleanup and push

- [ ] **Step 1: Add `.superpowers/` to `.gitignore`**

Create or append to `.gitignore`:
```
.superpowers/
node_modules/
```

- [ ] **Step 2: Full visual QA pass**

Open `http://localhost:8765` and verify:
- Sidebar renders all filters correctly
- Multi-select checkboxes filter with OR logic
- Cards show minimal badges + preview text
- Expanded cards show all detail fields
- URL state syncs correctly (share a filtered URL, reload)
- Mobile: filter button opens sidebar overlay
- Back-to-top button works
- Stats section toggle works
- Search highlighting works in card names and previews
- Keyboard navigation (Tab to cards, Enter/Space to expand)
- Skip link works

- [ ] **Step 3: Commit and push**

```bash
git add .gitignore index.html
git commit -m "feat: UX redesign — sidebar filters, simplified cards, preview text

Replaces horizontal filter bar with a sticky sidebar containing
checkbox groups (multi-select with OR logic), simplifies card
headers to FLAGGED/NEW badges only, and adds two-line description
previews that prioritize class annotations over DHS problem
statements."
git push origin master
```
