# SoCal Sky Now — live airport weather

**Live page:** https://jeffge911.github.io/SSCI-591-Project-3/
**Repository:** https://github.com/JeffGe911/SSCI-591-Project-3

SSCI 591 Web & Mobile GIS · Project 3 · Yuqi (Jeff) Ge · Fall 2026

## What this map shows

Every airport weather station that reports a METAR observation, centred on Southern California and zoomable to the whole world. Each station is coloured by its current *flight category* (VFR, MVFR, IFR or LIFR), the aviation shorthand for how good the flying weather is right now. Clicking a station shows live temperature, wind, visibility, humidity, sky condition and the observation time. The layer is a live feed that refreshes about once an hour, so the page changes on its own between the morning fog and the afternoon.

## Starting sample

[Intro to FeatureLayer](https://developers.arcgis.com/javascript/latest/sample-code/intro-featurelayer/) from the ArcGIS Maps SDK for JavaScript 5.1 gallery. It loads one feature service with `FeatureLayer`, gives it a renderer and a popup, and uses the `<arcgis-map>` component.

## What I changed

1. **Dataset.** Replaced the Warren Wilson College trees service with the NOAA METAR live feed, and deleted the sample's `fieldConfigurations`, `orderBy` and size visual variable because they referenced tree fields that no longer exist.
2. **Center, zoom and basemap.** The map opens on Southern California (`center="-118.25, 33.95" zoom="8"`) on the free `dark-gray-vector` basemap, with a toggle to `satellite`, instead of zooming to the layer's full (worldwide) extent.
3. **Renderer.** A `unique-value` renderer on `FLT_CATEGORY` with the standard aviation colours (green VFR, blue MVFR, red IFR, magenta LIFR) and a grey default.
4. **Popup.** A new `popupTemplate` titled `{STATION_NAME} ({ICAO})` with nine fields, labelled in plain English and formatted (no decimals on temperature, a readable date for `OBS_DATETIME`).
5. **Page.** New `<title>`, heading and "About this map" paragraph, `arcgis-map { height: 75vh }`, and a status line that runs a `queryFeatures` statistics query to count the stations on screen and show the newest observation time whenever the view stops moving.

## Data source

**NOAA METAR current wind speed and direction (Stations layer)**, an ArcGIS Living Atlas live feed maintained by Esri from NOAA data.
URL: https://services9.arcgis.com/RHVPKKiFTONKtxq3/arcgis/rest/services/NOAA_METAR_current_wind_speed_direction_v1/FeatureServer/0

Loaded with **Option 1, `FeatureLayer` + an ArcGIS REST service URL**. I picked it because it is genuinely live, which makes the point of the project visible: nothing in the repository changes, yet the map does. A feature service also lets the renderer and the statistics query run on the server, which matters for several thousand points worldwide. Before coding I opened the REST page and a GeoJSON query (`/query?where=ICAO='KLAX'&outFields=*&f=geojson`) to confirm the field names and their exact case.

## Component I added

`<arcgis-legend>` inside an `<arcgis-expand slot="bottom-left" expanded>`, so the legend explains the four colours but folds away on a phone. I also added `<arcgis-search>`, `<arcgis-home>` and `<arcgis-basemap-toggle>`.

## Problems and what I'd improve next

- **Time zones.** The server stores UTC and the browser converts it; I would label the zone explicitly in the popup.
- **Overlapping dots** around Los Angeles at zoom 8. I would add `featureReduction: { type: "cluster" }`.
- **Stale stations.** Some airports report an hour later than their neighbours; a `definitionExpression` hiding observations older than three hours would be more honest.
- **Next layer.** My Project 4 app is about LA vs San Diego air quality; adding the EPA AirNow live feed here would close the loop between the two projects.

## Credits

- Data: NOAA National Weather Service METAR observations, published as a live feed by Esri in ArcGIS Living Atlas of the World.
- Sample: Esri, *Intro to FeatureLayer*, ArcGIS Maps SDK for JavaScript 5.1; component tags from the *Legend component* sample and the *Get started* page. Basemaps by Esri.
- AI tools: I used Claude (Anthropic) to look up the 5.1 boilerplate and the layer's field list, to draft the renderer and popup code, and to proofread this README. I reviewed and tested every line and can explain the code.
