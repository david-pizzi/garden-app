# Garden App — Improvement Plan

Prioritised from feedback. Low effort / high value first.

---

## P1 — Quick wins (< 1 hour each)

### 1. Rename "Crop type" → "Growth method" + add helper text
**Effort:** XS | **Value:** High — reduces confusion immediately

- [x] Rename the label in `renderDetail()`
- [x] Add helper line beneath the dropdown:
  - Direct sown → "Harvest estimate uses sow date"
  - Transplanted → "Harvest estimate uses transplant date"

---

### 2. Combine status tags into a single readable line
**Effort:** S | **Value:** High — reduces cognitive load on cards

- [x] Remove separate `cropTypeTag` and `tag-day` chips from card
- [x] Remove separate overdue/germOverdue tag chips
- [x] Replace with combined `Day X • Next action label` status chip

---

### 3. Add "Next action" badge per card
**Effort:** S | **Value:** Very high — directly answers "what do I do next?"

- [x] Add `nextAction(plant)` helper with priority logic:
  - 🔴 Feed overdue
  - 🟢 Harvest now / overdue
  - 🟡 Harvest in ≤ 5d or Feed today/tomorrow
  - 🔵 Harvest / Feed coming up
- [x] Render as coloured status chip combined with Day X
- [x] Use next-action urgency for card border highlight colour

---

## P2 — Medium effort (half day each)

### 4. Collapsed cards by default
**Effort:** M | **Value:** High — biggest fix for scrolling

- [ ] Add `expandedIds: new Set()` to `state`
- [ ] Default: show only plant name + status chip (collapsed)
- [ ] Tap card body → toggle expanded (shows date row + quick actions)
- [ ] Move "open detail" to plant name / chevron tap

---

### 5. Collapsible sections on detail page
**Effort:** M | **Value:** Medium — hides empty fields, less overwhelming

- [ ] Add `expandedSections` map to `state`
- [ ] Render section headers as toggle buttons (▼ / ▶)
- [ ] Only "General" and sections with data open by default

---

### 6. "Missing data" intentional states
**Effort:** M | **Value:** Medium — removes friction for pre-app data

- [ ] Add optional `unknownFields: string[]` per plant
- [ ] Render `[ Unknown ] [ Estimated ]` toggle beneath date inputs
- [ ] Suppress overdue warnings for unknown fields
- [ ] Show "?" instead of "—" for unknown fields

---

## P3 — Larger features (future sprints)

### 7. Timeline / history view
- [ ] Build scrollable timeline card in detail view showing all event types (not just notes — see CLAUDE.md known issue)

### 8. Today-only mode
- [ ] Add `state.view = "today"` showing only plants needing action today

### 9. One photo per plant
- [ ] Store base64 image or URL per plant
- [ ] Show thumbnail on card

### 10. Quick log from home screen
- [ ] Extend quick-action buttons to write to `events[]` with a detail string

---

## P4 — End-of-season / nice-to-have

- [ ] "Would grow again?" rating (1–3 emoji) stored after harvest
- [ ] End-of-season stats: germination rate, avg days to harvest, total harvests
- [ ] Print stylesheet for detail page

---

## What to leave alone

Per feedback: visual style, dashboard layout (Overdue / Today / This week), location grouping, sow→germinate→transplant→feed→harvest workflow, harvest calculation logic are all working well.
