# SmartPark

SmartPark is a responsive, browser-based demonstration dashboard for monitoring public park dustbins. It uses six realistic sample records and simulates sensor changes every 30 seconds. A visible **DEMO MODE** label and the settings page make clear that no ESP32 devices are connected.

## Open the demo

Open `index.html` in a modern browser, or serve this folder with a local static web server. The map tiles and markers use Leaflet and OpenStreetMap; the icons and web fonts load from their public CDNs. Map tiles therefore need an internet connection. The app stores demo changes in browser local storage.

## Included screens

- Dashboard overview with record-derived totals, fill distribution, map preview, recent activity, and bin list.
- Live map with status filters, park/bin search, marker popups, focus controls, and configurable sample city center.
- Dustbin directory with text search, status filters, fill sorting, registration, editing, removal, and individual detail views.
- Alert center with unresolved/resolved history and resolve actions. Status thresholds can be changed in Settings.
- Collection queue sorted by fill level, collection history, and pickup actions that record a collection and reset the demo bin to empty.
- Analytics with fill trends, park comparisons, sensor status, fill calculation, and CSV export.
- Settings for city coordinates, thresholds, API shape, and planned future features.

## Demo data and sensor math

Fill percentage follows `((H - d) / H) × 100`, clamped to 0–100, where `H` is the internal bin depth and `d` is the ultrasonic distance reading. The current demo simulates percentage changes directly so the dashboard remains usable without an ESP32 or backend. Sample map locations are illustrative points near Chandigarh; configure a deployment city and replace each bin coordinate before real-world use.

## Backend integration outline

The frontend keeps its demo state in `app.js`; a production deployment should replace that store with a server API and persistent database. A practical integration boundary is:

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `POST` | `/api/v1/readings` | Accept `{ deviceId, distanceCm, binDepthCm, measuredAt }` from ESP32; calculate and store fill percentage and reading history. |
| `PATCH` | `/api/v1/bins/:id/connectivity` | Update a device's online state and last-seen timestamp. |
| `GET` | `/api/v1/bins` | Return the current bin directory and latest readings. |
| `GET` | `/api/v1/bins/:id/history` | Return time-stamped historical readings for details and analytics. |
| `GET` / `PATCH` | `/api/v1/alerts` and `/api/v1/alerts/:id` | List alerts and mark an alert resolved. |
| `POST` | `/api/v1/collections` | Record a bin pickup, worker, and collection time. |

The server should authenticate devices, validate readings and timestamps, store history, and create an alert when a reading crosses a configured status boundary. Hardware connectivity should be shown only when confirmed by the server.

## Files

- `index.html` — page structure and CDN dependencies.
- `styles.css` — responsive visual system and page layouts.
- `app.js` — sample records, demo simulation, map rendering, interactions, and local persistence.
