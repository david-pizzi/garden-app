# Garden App — Improvement Plan

Prioritised from feedback. Low effort / high value first.

---

## P1 — Quick wins (< 1 hour each)

### 1. Rename "Crop type" → "Growth method" + add helper text
**Effort:** XS | **Value:** High — reduces confusion immediately

- Rename the label and `<select>` option text in `renderDetail()` and `renderCard()`
- Add a small helper line beneath the dropdown:
  - Direct sown → "Harvest estimate uses sow date"
  - Transplanted → "Harvest estimate uses transplant date"
- No data model changes needed

---

### 2. Combine status tags into a single readable line
**Effort:** S | **Value:** High — reduces cognitive load on cards

Current: `day 19` + `🪴 Transplanted` as separate chips

Target: `Day 19 • Harvest in 6 days` (or `• Feed tomorrow`)

- Update `renderCard()` tag/date row logic
- Pick the single most actionable date (harvest > feed > germination) and surface it inline
- Remove redundant separate chips where the combined line covers it

---

### 3. Add "Next action" badge per card
**Effort:** S | **Value:** Very high — directly answers "what do I do next?"

Logic already exists in `feedStatus()`, `estimatedHarvestDate()`, `estimatedGerminationDate()`.
Wrap into a `nextAction(plant)` helper that returns one prioritised action:

| Priority | Condition | Label |
|---|---|---|
| 1 | Feed overdue | 🔴 Feed overdue |
| 2 | Harvest ready / overdue | 🟢 Harvest now |
| 3 | Feed due today/tomorrow | 🟡 Feed today / tomorrow |
| 4 | Harvest in ≤ 5 days | 🟡 Harvest in Xd |
| 5 | Nothing due this week | 🔵 Nothing for Xd |
| 6 | No data | _(blank)_ |

Render as a single coloured pill at the bottom of `plant-card-main`, above quick actions.

---

## P2 — Medium effort (half day each)

### 4. Collapsed cards by default
**Effort:** M | **Value:** High — biggest scrolling fix

- Add `expandedIds: new Set()` to `state`
- Default: show only plant name + next-action badge (collapsed)
- Tap card body → toggle expanded (shows full tags, date row, quick actions)
- Persist expanded set in `state` only (not localStorage — reset on refresh is fine)
- Quick-action buttons remain accessible in collapsed view (or shown always)

Implementation note: the click handler on `.plant-card` already exists; change it from "open detail" to "toggle expanded", and move "open detail" to the plant name or a `›` chevron tap.

---

### 5. Collapsible sections on detail page
**Effort:** M | **Value:** Medium — hides empty fields, less overwhelming

- Add `expandedSections: { general: true, germination: false, harvest: false, care: false }` to `state`
- Render section headers as toggle buttons (▼ / ▶)
- Only "General" and whichever section has data open by default
- Sections with no data set auto-collapse

---

### 6. "Missing data" intentional states
**Effort:** M | **Value:** Medium — removes guilt/friction for pre-app data

- Add `unknown` flag per date field (or a `dataQuality` map on each plant)
- Render a small `[ Unknown ] [ Estimated ]` toggle beneath date inputs
- When marked Unknown: show "?" instead of "—", suppress overdue warnings for that field
- Minimal data model change: add optional `unknownFields: string[]` array per plant

---

## P3 — Larger features (future sprints)

### 7. Timeline / history view
Per-plant chronological log of all events (already stored in `events[]`, just not fully shown — see known issue in CLAUDE.md). Build a scrollable timeline card in the detail view showing all event types, not just notes.

### 8. Today-only mode
A stripped-down view showing only plants that need action today (feeds, harvests, germination checks). Could be a third view (`state.view = "today"`).

### 9. One photo per plant
Store a single base64 image per plant in localStorage (or link to an external URL). Show as a small thumbnail on the card. Warn on storage size.

### 10. Quick log from home screen
Tap "Fed" / "Harvested" quick-action buttons → log event automatically (already partially done for fed/harvest — extend to write to `events[]` with a detail string).

---

## P4 — End-of-season / nice-to-have

- **"Would grow again?" rating** — 1–3 star or emoji rating stored on plant after harvest
- **End-of-season stats** — total harvests, germination rate, average days to harvest per crop type
- **Export to PDF / print** — simple print stylesheet on the detail page

---

## What to leave alone

Per feedback: the visual style, dashboard layout (Overdue / Today / This week), location grouping, the sow→germinate→transplant→feed→harvest workflow, and harvest calculation logic are all working well. Don't touch these.
