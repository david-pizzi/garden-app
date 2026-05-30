# Garden App — CLAUDE.md

## Project overview

Single-file vanilla JS garden tracker ("Violetta's Growing Journal"). No build step, no dependencies. Open `index.html` directly in a browser or via a local server (required for the "Load sample data" fetch).

## Files

- `index.html` — entire app: CSS, HTML shell, and all JS
- `sample-plants.json` — sample plant data, loaded via `fetch()` from the storage panel

## Architecture

The app uses a simple render-loop pattern:

1. `state` — plain object holding all runtime state (plants, current view, UI flags)
2. `render()` — writes HTML into `#app` via `innerHTML`, then calls `attachEvents()`
3. `attachEvents()` — re-attaches all event listeners after each render

There are two views: `"home"` and `"detail"`. View is toggled via `state.view`.

## Data model

Plants are stored as objects in `state.plants` and persisted to `localStorage` under key `violetta-garden-tracker-v1`. Snapshots use the prefix `violetta-snap-`.

Key plant fields: `id`, `name`, `variety`, `cropType` (`"sown"` | `"transplanted"`), `location`, `sowDate`, `germinationDate`, `germinationDays`, `transplantDate`, `lastFed`, `feedEvery`, `harvestDate`, `harvestDays`, `bedNumber`, `quantity`, `germinatedQuantity`, `events[]`.

New plants created via `confirmAddPlant()` omit `cropType`, `transplantDate`, `bedNumber`, `harvestDays`, `harvestDate`, `harvestTemp` — these default to `undefined`/`null` and are handled defensively throughout the code.

## Key conventions

- All user-supplied strings rendered into HTML go through `esc()` (XSS prevention)
- Dates are stored as `YYYY-MM-DD` strings; parsed with `new Date(dateStr)` (UTC midnight — see known issue below)
- `render()` must be called after any state mutation that affects the UI
- Event log is capped at 30 entries per plant (`logEvent` → `.slice(0, 30)`)

## Known issues / gotchas

- **Timezone off-by-one**: `new Date("YYYY-MM-DD")` parses as UTC midnight. `daysSince()` and `formatDate()` can show the wrong day in UTC− timezones.
- **`bed-number-input` change doesn't call `render()`** (`attachEvents` line ~1466): bed number saves but the card doesn't refresh until the next interaction.
- **New plant objects are incomplete**: `confirmAddPlant()` doesn't include `cropType`, `harvestDays`, `harvestDate`, `harvestTemp`, `transplantDate`, `bedNumber`. Add these if extending plant creation.
- **Silent `localStorage` failures**: `savePlants()` swallows errors — if storage is full the save silently fails.
- **Non-note events not displayed**: `sample-plants.json` includes `"Sown"`, `"Germinated"`, etc. event types, but the detail view only renders `type === "note"` events. The richer event history is stored but invisible.

## Development

No build step needed. Edit `index.html` and refresh the browser.

To serve locally (needed for sample data fetch):
```
npx serve .
# or
python3 -m http.server 8080
```
