# London's 19th Century Industrial Geography

A static, GitHub-Pages-ready swipe map comparing London **c.1865–75** with
**c.1893–95**: industrial sites, docks, and watercourses on the Ordnance
Survey first series vs. the 1890s revision.

No ESRI, no build step, no server-side code — just `index.html` + GeoJSON.

---

## View locally

**You cannot just double-click `index.html`.** Browsers block the
`fetch()` calls that load the GeoJSON when the page is opened with a
`file://` URL — you'll see only the basemap and the panel boxes, no data.
Serve the folder over HTTP:

```bash
cd docs
python3 -m http.server 8000
# then open http://localhost:8000/ in your browser
```

Any static server works — `npx serve`, VS Code's Live Server, `php -S`, etc.

## Deploy to GitHub Pages

The site is organised so GitHub Pages can serve it from `/docs` without
any workflow configuration:

1. Push this repo to GitHub.
2. Repo → **Settings → Pages** → source `Deploy from a branch`,
   branch `main`, folder `/docs`.
3. In a minute or two your map will be live at
   `https://<you>.github.io/london_industry_map/`.

## Repo layout

```
london_industry_map/
├── README.md         # this file
├── .gitignore
└── docs/             # the deployed site
    ├── index.html    # the app
    └── data/
        ├── Industry_1865-75.geojson    # LEFT side  (primary swipe)
        ├── Industry_1893-95.geojson    # RIGHT side (primary swipe)
        ├── Water_First_Series.geojson  # LEFT side  context
        ├── Water_1895.geojson          # RIGHT side context
        ├── Docks.geojson               # split by London1stSeries/Revision flag
        ├── TQ_TidalWater.geojson       # shared (Thames) — clipped to London bbox
        ├── Lower_River_Lea.geojson     # shared
        ├── LCC_Border.geojson          # always-on boundary
        ├── CityOfLondon.geojson        # always-on boundary
        ├── Areas_1911.geojson          # extra (not loaded by default)
        ├── GreaterLondon_1851.geojson  # extra (not loaded by default)
        └── WestHam_1911.geojson        # extra (not loaded by default)
```

All GeoJSON is **EPSG:4326** (WGS84 lat/lon), coordinates rounded to 6
decimal places (~0.1 m precision) to keep file sizes small.

## How the swipe works

Two Leaflet map panes — `leftPane` and `rightPane` — each get their own
SVG renderer. The early (1865–75) layers are drawn into `leftPane` and
the late (1893–95) layers into `rightPane`. A draggable `<div>` sets a
CSS `clip-path: inset(...)` on each pane's SVG element so each side only
shows content up to the divider. Shared context (Thames, Lea, LCC
border, City of London) sits on the default overlay pane and stays
visible across the whole map.

This works with vector (GeoJSON) layers, which the popular
`leaflet-side-by-side` plugin doesn't — it clips DOM containers by class
and assumes TileLayer-style `.getContainer()`.

## Credits

By **[Jim Clifford](https://jimclifford.ca/)**, with help from Steven
Langlois and Anne Riitta Janhunen.

Data archive: [Zenodo](https://zenodo.org/) *(replace with the specific
record URL/DOI once published)*.

Source data: Ordnance Survey first series (c.1865–75) & 1890s revision.

## Basemaps

Default: **CartoDB Positron** — clean modern minimal.

Also in the layer switcher:

* Esri World Shaded Relief — pure grayscale hillshade only
* Minimal (Positron No-Labels) — coastlines + water only, no placenames
* [NLS OS 1-inch 2nd edition (c.1895)](https://maps.nls.uk/) —
  period-matched historical overlay

Other basemap variables (Stadia Alidade/Stamen, Esri Physical/Terrain,
OpenTopoMap, OSM) are still defined in `docs/index.html` but omitted
from the switcher. Add them back to the `L.control.layers({...})`
object if you want more options.

Browse every free Leaflet-ready basemap live at
<https://leaflet-extras.github.io/leaflet-providers/preview/>.
