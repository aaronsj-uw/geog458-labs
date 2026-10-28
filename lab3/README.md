Lab 3: Map Design and Tile Generation

Aaron Jacowitz, GEOG 458

This lab contains two web maps. Each one combines a basemap I styled in Mapbox Studio, a thematic raster I interpolated and styled in QGIS, and the monitoring stations as interactive vector points. Both rasters were exported from QGIS as local XYZ tile pyramids with QMetaTiles and are displayed with MapLibre GL JS, so the web pages load their tiles from this repository instead of a live Mapbox map service.

Map	Page	Topic
Guided	aaronjacowitz_lab3-1.html	Fine particulate matter (PM2.5), central Puget Sound, July 5, 2025
Independent	aaronjacowitz_lab3-2.html	Ground-level ozone, Western Washington, May 30, 2025
Folder structure
lab3/
├── aaronjacowitz_lab3-1.html
├── aaronjacowitz_lab3-2.html
├── README.md
├── screenshots/
├── data/
│   ├── pm25_puget_sound_2025-07-05.csv
│   ├── pm25_stations.geojson
│   ├── pm25_source.json
│   ├── ozone_western_wa_2025-05-30.csv
│   ├── ozone_stations.geojson
│   └── ozone_source.json
└── assets/
    ├── mapbox-guided/{z}/{x}/{y}.png
    ├── pm25-guided/{z}/{x}/{y}.png
    ├── mapbox-independent/{z}/{x}/{y}.png
    └── ozone/{z}/{x}/{y}.png
Map 1 (guided): Puget Sound PM2.5

Guided PM2.5 map

Purpose. The map shows how fine particulate matter varied across the central Puget Sound region on one summer day. It is meant for residents and students who want to see where measured PM2.5 was higher or lower and how the monitors relate to the estimated pattern between them.

Study area. Central Puget Sound from Tacoma to Marysville, including Seattle and Bremerton. Longitude -122.75 to -122.05, latitude 47.10 to 48.15.

Sources.

PM2.5: U.S. EPA AirData pre-generated daily file daily_88101_2025.zip, prepared by the course as an 11-station sample for 2025-07-05 (24-hour block average, one instrument per site). Values range from 4.1 to 12.1 µg/m³. See data/pm25_source.json.
Basemap: my Mapbox Studio style lab3-air-quality-aaronjacowitz, based on the classic Mapbox Light style. © Mapbox © OpenStreetMap.

Processing.

In Mapbox Studio, I cloned the classic Light style and filtered the poi-label layer to exclude five commercial POI classes (commercial_services, store_like, food_and_drink, food_and_drink_stores, lodging) so the background stays quiet behind the PM2.5 colors. Schools, hospitals, parks, and place and road labels remain.
I connected the published style to QGIS through the Mapbox WMTS service, set the project to EPSG:3857, and exported the basemap with QMetaTiles to assets/mapbox-guided (256 px PNG, XYZ, opaque background).
I loaded the 11 stations, reprojected them to EPSG:32610 (UTM zone 10N) so distances are in meters, and ran QGIS IDW interpolation on pm25 with a distance coefficient of 2 and a 250 m pixel size.
I styled the raster as a continuous pseudocolor ramp from 4 to 14 µg/m³ (stops at 4, 6.5, 9, 11.5, 14; yellow to dark red) and exported it alone to assets/pm25-guided with a transparent background.
In the web page, the PM2.5 tiles sit above the basemap at 70% opacity, and the station GeoJSON is drawn on top with popups.

Exported extent and zoom range. Both pyramids cover longitude -122.75 to -122.05 and latitude 47.10 to 48.15 at zoom levels 8 to 12.

Map 2 (independent): Western Washington ozone

Independent ozone map

Purpose. Ground-level ozone is not emitted directly. It forms when pollution from vehicles and industry reacts in sunlight, so it often peaks downwind of a city rather than in it. This map is meant for residents and anyone planning outdoor activity in Western Washington who wants to see that pattern. On May 30, 2025, the Seattle Beacon Hill monitor measured 44 ppb, while monitors to the east and southeast measured more: Enumclaw 56 ppb, Issaquah 52 ppb, and North Bend 51 ppb.

Study area. Western Washington from Vancouver to the Canadian border. Longitude -123.3 to -121.3, latitude 45.5 to 49.05. I used a larger area than the guided map because only 4 ozone monitors are in King County, which is too few for a meaningful interpolation. Western Washington has 9.

Sources.

Ozone: U.S. EPA AirData, Download Daily Data tool (pollutant: Ozone, year: 2025, area: Washington, all sites). See data/ozone_source.json.
Basemap: my Mapbox Studio style lab3-ozone-aaronjacowitz, based on the classic Mapbox Streets style. © Mapbox © OpenStreetMap.

Processing.

From the EPA download, I kept monitors west of 121° W and chose 2025-05-30 because it was the date with the most Western Washington monitors reporting (9) and the highest average concentration. I kept one reading per site, the daily maximum 8-hour average, and converted it from ppm to ppb (ppm × 1000) so the values are easier to read. The result is data/ozone_western_wa_2025-05-30.csv and data/ozone_stations.geojson.
In Mapbox Studio, I started from the classic Streets style, turned off poi-label because individual businesses do not help explain a regional ozone pattern.
I exported the published style from QGIS to assets/mapbox-independent (EPSG:3857, 256 px PNG, XYZ, opaque background).
I reprojected the monitors to EPSG:32610 and ran IDW on ozone_ppb with a distance coefficient of 2 and a 500 m pixel size. The larger pixel size suits the larger study area.
I styled the raster with a continuous pink-to-purple ramp from 35 to 55 ppb (stops at 35, 40, 45, 50, 55) so it is clearly different from the PM2.5 map, and exported it alone to assets/ozone with a transparent background.
The web page reuses the guided map's structure: basemap tiles, ozone tiles at 70% opacity, then the monitors with popups showing the measured value, daily AQI, date, and site ID.

Exported extent and zoom range. Both pyramids cover longitude -123.3 to -121.3 and latitude 45.5 to 49.05 at zoom levels 7 to 11. I used one zoom level lower than the guided map because the area is much larger, which keeps the number of tiles and the repository size manageable.

Estimates versus measurements

The colored surfaces on both maps are estimates. IDW fills the space between monitors by giving nearby monitors more weight than distant ones, but it does not add new measurements. With only 11 PM2.5 monitors and 9 ozone monitors, the surfaces are smooth guesses that cannot show local hot spots between stations, and the ozone surface in particular spans large areas with no monitor at all. The raster cell size (250 m or 500 m) controls how fine the image is, not how accurate it is.

The station points are the measurements. Clicking a station shows the value that monitor actually recorded for that day. Clicking the colored surface does not return a value, because the tiles are pre-rendered PNG images, not data. Neither map shows AQI categories, and neither should be used as an official air-quality assessment.

Tools

Mapbox Studio, QGIS 3 with the QMetaTiles plugin, MapLibre GL JS 6.15.0, VS Code with Live Server, and GitHub Pages.