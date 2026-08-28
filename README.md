# 🚛 FMC003 Vehicle Tracking Dashboard

A single-file web dashboard for a **Teltonika FMC003** GPS tracker connected through **[Flespi](https://flespi.io)**. Real-time telemetry, historical trip replay on a map, configurable alerts and geofencing — no backend and no build step required.

## Features

### 📡 Live mode
- Auto-refreshing telemetry cards: speed, ignition, movement, CAN speed/RPM/fuel, device battery, external power, GSM signal, GPS satellites, altitude, heading, mileage, device temperature
- Table rendering **every parameter** the tracker sends (nothing is hardcoded — new FMC003 parameters appear automatically)
- Live map marker that rotates with heading, breadcrumb trail, info popup

### 🕘 History mode
- Fetch all messages for any date range (quick ranges: Today / Yesterday / Last 7d / Last 30d)
- Automatic **trip detection** (splits on 8-minute data gaps or 3+ minutes stationary; sub-100 m segments discarded as GPS noise)
- Trip list with distance, duration, average and max speed
- Speed-colored track on the map (green → red), start/end flags, click any point of the track for time/speed/direction
- Speed chart and **trip playback** at 2x–40x, CSV export per trip

### 🔔 Alerts
| Alert | Trigger |
|---|---|
| ⚡ Overspeed | Speed crosses a configurable limit (default 120 km/h) |
| 🔑 Ignition ON | Engine started |
| 🔋 Low battery | Device battery < 3.6 V and/or vehicle battery < 11.8 V (configurable) |
| ⭕ Geofence | Enter / exit of any circular geofence |

- Alerts drawer with rule toggles and thresholds, badge with unseen count, persistent alert log (last 300), toasts and browser notifications
- **Geofence editor**: click *Add geofence*, click the map, name it, set the radius — circles persist on the map
- History loads are scanned for alert events with their real timestamps (deduplicated)

## Quick start

1. In [Flespi](https://flespi.io): create a **token** (Tokens → `+`). For security, use an ACL token limited to `GET` on your device.
2. Note your **device ID** (visible in the device URL/panel).
3. Open `index.html` in any modern browser (double-click works — no server needed).
4. Click **⚙️ Settings**, paste the token and device ID, save.

Configuration is stored in your browser's `localStorage` only.

## Deploy (optional)

Host it anywhere static files are served. With GitHub Pages: **Settings → Pages → Deploy from a branch → main / (root)**, then open `https://<user>.github.io/fmc003-vehicle-tracking-dashboard/`.

> ⚠️ The page calls the Flespi API directly from the browser, so anyone with access to the page can read the token from localStorage. Use a restricted ACL token, or put the dashboard behind authentication.

## How it talks to Flespi

| Endpoint | Used for |
|---|---|
| `GET /gw/devices/{id}` | Device name / ident |
| `GET /gw/devices/{id}/telemetry/all` | Live view (latest values of all parameters) |
| `GET /gw/devices/{id}/messages?data={"from":..,"to":..,"count":1000}` | History, trips and alert scanning (auto-paginated) |

## Tech

Vanilla HTML/CSS/JS + [Leaflet](https://leafletjs.com/) with OpenStreetMap tiles. No dependencies to install, no build step.
