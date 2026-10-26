# SoCal Sky Now — live airport weather

**Live page:** https://jeffge911.github.io/SSCI-591-Project-3/
**Repository:** https://github.com/JeffGe911/SSCI-591-Project-3

SSCI 591 Web & Mobile GIS · Project 3 · Yuqi (Jeff) Ge · Fall 2026

## What this map shows

Every airport weather station reporting a METAR observation, centred on Southern California and zoomable to the whole world. Each dot is coloured by its current flight category (VFR, MVFR, IFR, LIFR). Clicking a station shows live temperature, wind, visibility, sky condition and observation time. The feed refreshes about once an hour, so the map changes on its own.

## Starting sample

[Intro to FeatureLayer](https://developers.arcgis.com/javascript/latest/sample-code/intro-featurelayer/), ArcGIS Maps SDK for JavaScript 5.1.

## What I changed

1. **Dataset:** replaced the tree service with the NOAA METAR feed and removed the sample's `fieldConfigurations`, `orderBy` and size variable, which referenced old fields.
2. **Center, zoom, basemap:** opens on Southern California at zoom 8 on the free `dark-gray-vector` basemap, with a toggle to `satellite`.
3. **Renderer:** a `unique-value` renderer on `FLT_CATEGORY` using aviation colours.
4. **Popup:** titled `{STATION_NAME} ({ICAO})` with nine labelled, formatted fields.
5. **Page:** new title, heading and About paragraph, map height 75vh, and a status line that counts stations in view with a `queryFeatures` statistics query.

## Data source

NOAA METAR current wind speed and direction (Stations layer), an ArcGIS Living Atlas live feed.
https://services9.arcgis.com/RHVPKKiFTONKtxq3/arcgis/rest/services/NOAA_METAR_current_wind_speed_direction_v1/FeatureServer/0

Loaded as a `FeatureLayer` from the REST URL (Option 1). I chose it because it is genuinely live, and a feature service lets the renderer and statistics run on the server. I checked the field names and their case on the layer's REST page and with a GeoJSON query first.

## Component I added

`<arcgis-legend>` inside an `<arcgis-expand>` so it folds away on a phone, plus `<arcgis-search>`, `<arcgis-home>` and `<arcgis-basemap-toggle>`.

I also added a small status line under the title, which is the most complex part of the code. It is not a ready-made component; I wrote it myself. It asks the server two questions: how many stations are on the screen right now, and when the newest observation was taken. `createQuery()` sets up the question, `outStatistics` asks for a count and a latest time instead of every record, and `queryFeatures()` sends it. `reactiveUtils.when()` runs it again each time the map stops moving, so the number updates after you pan or zoom.

## Problems and what I'd improve next

The status line failed on first deploy because the statistics query ran before the view was ready; fixed by waiting for `view.stationary`. Next I would cluster overlapping stations around Los Angeles, hide observations older than three hours, and add the EPA AirNow feed to connect with my Project 4 air-quality app.

## Credits

Data: NOAA, published by Esri in ArcGIS Living Atlas. Sample and basemaps: Esri. AI tools: I used Claude (Anthropic) to look up the 5.1 boilerplate and field list, draft the renderer and popup code, and proofread this README. I reviewed and tested every line and can explain the code.
