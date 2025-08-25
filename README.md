# osm-app-menu
Generated Open Street Maps menus (US &amp; Canada) for the GeoSway Sheets add-on — JSON/CSV catalogs + Python builder (Overpass-only). Data via OpenStreetMap contributors; not affiliated or endorsed.

# GeoSway — OSM App Menu (US + Canada)

Generated catalogs of **administrative areas** from OpenStreetMap for use in the GeoSway Google Sheets add-on.  
Outputs include friendly, human-readable **level names** (e.g., _States_, _Counties_, _Parishes_, _Municipalities_) alongside `admin_level` for easy menu building.

**Data via OpenStreetMap contributors (ODbL). Not affiliated with or endorsed by OpenStreetMap or the OSMF.**

---

## What’s in here

- **`out/`** — published JSON/CSV you can load directly in the add-on  
  - `out/manifest.json` (top-level index)
  - `out/US/states.json|csv` (US states/DC/territories)
  - `out/US/by_state/<ST>/{4,6,7,8,9,10}.json|csv`
  - `out/CA/states.json|csv` (Canadian provinces/territories)
  - `out/CA/by_state/<PR>/{4,6,7,8,9,10}.json|csv`
- **`generate_osm_admin_manifest_us_ca.py`** — Python script that builds the catalogs (Overpass-only, mirror failover, backoff, lightweight `out tags;` queries)
- **GitHub Actions** workflow in `.github/workflows/osm-catalog.yml` to rebuild on a schedule and commit `/out`.

> Default levels emitted: **4, 6, 7, 8, 9, 10**. You can trim to `4,6,8` if you want a smaller menu.

---

## Friendly names (used in the files’ `level_name` field)

**United States**
- **4** – `States/DC/Territories`
- **6** – `Counties/County-equivalents`  
  _Overrides_: **LA** → `Parishes`; **AK** → `Boroughs/Census Areas`
- **7** – `Townships/Boroughs/Districts`
- **8** – `Cities/Towns/Villages/Municipalities`
- **9** – `Wards/Districts`
- **10** – `Neighborhoods/Hamlets`

**Canada**
- **4** – `Provinces/Territories`
- **6** – `Counties/Regional Districts/Divisions`
- **7** – `Subdivisions/Municipal Districts/Regional Municipalities`
- **8** – `Municipalities/Cities/Towns/Villages`
- **9** – `Wards/Districts`
- **10** – `Neighbourhoods/Hamlets/Localities`

> Friendly names are a best-effort mapping of OSM’s `boundary=administrative` + `admin_level=*` and can vary by province/state.

---

## Consume in your Sheets add-on

**Option A — GitHub Pages** (recommended)
```js
// Apps Script example
const ROOT = 'https://geosway.github.io/osm-app-menu';
const manifest = JSON.parse(UrlFetchApp.fetch(`${ROOT}/manifest.json`).getContentText());

// Load countries, then states/provinces
const caMeta = JSON.parse(UrlFetchApp.fetch(`${ROOT}/CA/manifest.json`).getContentText());
const caStates = JSON.parse(UrlFetchApp.fetch(`${ROOT}/CA/states.json`).getContentText());

// For a province (e.g., AB) and level 8:
const fileRel = caMeta.by_state['AB'].files['8'].json; // e.g., "by_state/AB/8.json"
const munis = JSON.parse(UrlFetchApp.fetch(`${ROOT}/CA/${fileRel}`).getContentText());
