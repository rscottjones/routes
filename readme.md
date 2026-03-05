# Routes for Micro.blog

An interactive route map plugin for [Micro.blog](https://micro.blog) that renders GPS tracks on a map with a stats bar, elevation profile, waypoint markers, and more. Built on [Leaflet.js](https://leafletjs.com/) and [leaflet-gpx](https://github.com/mpetazzoni/leaflet-gpx).

Companion to [Maps for Micro.blog](https://github.com/moonmehta/maps-for-microblog) — both plugins share the same basemap options and can coexist on a single page.

## Example uses

- Post about a hike and show the route on a topo map with a stats bar displaying distance, elevation gain, and moving time.
- Log your cycling season with a multi-route map showing every ride in a different color, with each route linking to that week's post.
- Share a race course with aid station waypoints, distance markers, and a crew access point — all on a single map.
- Document a road trip by plotting the driving route on a NatGeo-style map with photo waypoints that open in a lightbox.
- Map a paddling route with put-in, take-out, portage, and hazard markers.
- Post your ski day with runs overlaid on OpenSnowMap, each colored by difficulty.
- Show the full W Trek as five daily routes on one map, each in a different color with a legend linking to the trip report for each day.
- Embed a trail race course with per-mile markers so runners can visualize the splits before race day.
- Plot a multi-day cycling tour with camp waypoints, water sources, and bike repair stops.
- Share a scenic drive with viewpoint and photo waypoints — click any waypoint name to open the photo in a full-screen lightbox.

## Features

- **GPX-powered** — Load any GPX file exported from Strava, Garmin Connect, AllTrails, Komoot, Apple Fitness, or any other fitness or mapping app.
- **Stats bar** — Automatically displays distance, elevation gain and loss, and moving time from the GPX data below the map.
- **Elevation profile** — Optional SVG chart rendered below the stats bar, showing the route's elevation curve with gradient fill, grid lines, and labeled axes.
- **Waypoints** — Place markers along a route using a separate YAML file. Supports 20+ built-in icons, numbered circles, and emoji. Photo URLs open in a full-screen lightbox.
- **Multi-route maps** — Display multiple GPX tracks on one map, each with its own color and popup link. An auto-palette assigns colors when none are specified.
- **Route legend** — Optionally display a color-swatch key below a multi-route map identifying each route by name.
- **Start/finish markers** — Automatically placed at the endpoints of each route. Loop detection suppresses the duplicate finish marker on circular routes.
- **14 basemaps** — Includes topo, cycling, USGS, satellite, shaded relief, snow, NatGeo, and more. No API key required.
- **Route simplification** — Reduces GPX point density for faster rendering on long routes or multi-route maps, using the Ramer-Douglas-Peucker algorithm.
- **Lightbox** — Waypoint URLs pointing to image files open in a full-screen overlay with gallery navigation, keyboard support, and swipe gestures.
- **Self-contained** — No head partials, footer partials, or microhooks required. Works with any Micro.blog theme.
- **Maps plugin compatible** — Safe to use alongside Maps for Micro.blog on the same page. Leaflet loads only once and both plugins share the same lightbox instance.

## Installation

### From the Micro.blog plugin directory

1. Go to **Account → Plug-ins → Find Plug-ins** and search for "Routes for Micro.blog." Click **Install**.
2. Create a `data/routes/` folder in your custom theme and add one or more YAML data files. See [Data File Format](#data-file-format) below.
3. Upload your GPX files via the media library (**Posts → Edit → file icon**).
4. Add the shortcode to any post or page. See [Usage](#usage) below.

### Manual installation

1. Copy the contents of `route.html` into your blog's custom theme as a new template file at `layouts/shortcodes/route.html`.
2. Create a `data/routes/` folder in your custom theme and add your YAML data files.
3. Add the shortcode to any post or page.

After installation your custom theme should include:

```
layouts/
  shortcodes/
    route.html
data/
  routes/
    w-trek-day1.yml         ← route data
    w-trek-waypoints.yml    ← optional waypoints
```

## Usage

### Basic

```
{{< route "w-trek-day1" >}}
```

This renders a map using the route defined in `data/routes/w-trek-day1.yml`.

### Multiple route maps on one page

```
{{< route "w-trek-day1" >}}
{{< route "w-trek-day2" >}}
```

### Optional parameters

```
{{< route source="w-trek-day1" basemap="topo" >}}
{{< route source="w-trek-day1" height="600px" stats="false" >}}
{{< route source="w-trek-day1" elevation="true" >}}
{{< route source="w-trek-day1" color="#E11D48" weight="5" >}}
{{< route source="w-trek-day1" waypoints="w-trek-waypoints" >}}
{{< route source="w-trek-day1" markers="false" >}}
{{< route source="w-trek-day1" simplify="true" >}}
{{< route source="patagonia-hikes" legend="true" >}}
{{< route source="patagonia-hikes" color="SteelBlue" >}}
```

| Parameter | Default | Description |
|---|---|---|
| `source` | *(required)* | Name of the YAML data file in `data/routes/` (without `.yml`). |
| `height` | `500px` | Height of the map container. Any valid CSS value works. A bare number is treated as pixels. |
| `color` | `#E11D48` | In single-route mode: default route line color. In multi-route mode: forces all routes to this color, overriding per-route colors and the auto-palette. Omit to use per-route colors. |
| `weight` | `4` | Route line width in pixels. |
| `basemap` | `osm` | Base map tile style. See [Base Maps](#base-maps). |
| `stats` | `true` | Show the stats bar below the map. Single-route mode only. |
| `elevation` | `false` | Show an SVG elevation profile below the stats bar. Single-route mode only. Only meaningful if the GPX contains elevation data. |
| `markers` | `true` | Show start and finish markers at the endpoints of each route. Set to `false` for reference routes where endpoints are meaningless. |
| `waypoints` | *(none)* | Name of a waypoint YAML file in `data/routes/` (without `.yml`). Single-route mode only. Overrides the `waypoints` field in the data file. |
| `legend` | `false` | Show a color-swatch legend below the map. Multi-route mode only. |
| `simplify` | `false` | Reduce GPX point density for faster rendering. Use `"true"` for a sensible default, or a numeric tolerance in degrees (e.g. `"0.00003"`). |

## Data File Format

Data files live in `data/routes/` in your custom theme. The plugin detects single-route vs. multi-route format automatically from the YAML structure.

### Single-route format

A top-level YAML map. Only `gpx` is required.

```yaml
gpx: "/uploads/2025/w-trek-day1.gpx"
name: "W Trek — Day 1"
url: "/2025/05/w-trek-day1"
description: "Grey Glacier to Paine Grande. 11 km, 340 m gain."
color: "#E11D48"
weight: 4
basemap: "topo"
stats: true
elevation: true
markers: true
simplify: false

waypoints: "w-trek-waypoints"
```

Any field set in the data file can be overridden by the corresponding shortcode parameter.

#### Single-route field reference

| Field | Required | Description |
|---|---|---|
| `gpx` | Yes | Path to the GPX file. Must be hosted on your blog (see [GPX Files](#gpx-files)). |
| `name` | No | Route name. Shown in the stats bar and in the route popup when clicked. |
| `url` | No | Link destination. Makes the route line clickable, opening a popup with the name, description, and a link. |
| `description` | No | Short text shown in the stats bar and route popup. |
| `color` | No | Route line color. Accepts CSS color names or hex values. Overrides the shortcode `color` param. |
| `weight` | No | Line width in pixels. Overrides the shortcode `weight` param. |
| `basemap` | No | Base map style. Overrides the shortcode `basemap` param. See [Base Maps](#base-maps). |
| `stats` | No | Show or hide the stats bar. Overrides the shortcode `stats` param. |
| `elevation` | No | Show or hide the elevation profile. Overrides the shortcode `elevation` param. |
| `markers` | No | Show or hide start/finish markers. Overrides the shortcode `markers` param. |
| `simplify` | No | Route simplification. Overrides the shortcode `simplify` param. |
| `waypoints` | No | Name of a waypoint YAML file in `data/routes/` (without `.yml`). |

---

### Multi-route format

A top-level YAML list. Each entry is a separate GPX track. Only `gpx` is required per route.

```yaml
- name: "W Trek — Day 1"
  gpx: "/uploads/2025/w-trek-day1.gpx"
  color: "#E11D48"
  url: "/2025/05/w-trek-day1"
  description: "Grey Glacier to Paine Grande. 11 km, 340 m gain."

- name: "W Trek — Day 2"
  gpx: "/uploads/2025/w-trek-day2.gpx"
  color: "#7C3AED"
  url: "/2025/05/w-trek-day2"
  description: "Paine Grande to Italiano. 9 km, 180 m gain."

- name: "Park boundary"
  gpx: "/uploads/2025/park-boundary.gpx"
  color: "#94a3b8"
  popup: false
  markers: false
```

In multi-route mode:
- The stats bar and elevation profile are never shown.
- Each route gets a popup on click showing its name, description, and link (unless `popup: false`).
- If `color` is omitted, the plugin cycles through a built-in palette of 10 distinct colors.
- Passing `color=` in the shortcode forces all routes to that color.
- Hovering a route brings it to the front, keeping overlapping routes clickable.

#### Multi-route field reference

| Field | Required | Description |
|---|---|---|
| `gpx` | Yes | Path to the GPX file. |
| `name` | No | Route name shown in popups and the legend. |
| `url` | No | Makes the route line clickable with a popup link. |
| `legend_url` | No | URL for this route's legend label. Falls back to `url` if omitted. |
| `description` | No | Short text shown in the route popup. |
| `color` | No | Route line color. If omitted, the auto-palette assigns one. |
| `weight` | No | Line width in pixels. |
| `popup` | No | Set to `false` to suppress the click popup for this route. Useful for reference lines. |
| `markers` | No | Set to `false` to suppress start/finish markers for this route. |

---

### Waypoint format

A top-level YAML list. Stored in a separate file in `data/routes/` and referenced from the route data file or shortcode. Single-route mode only.

```yaml
- name: "Refugio Grey"
  lat: -50.9876
  lon: -73.1234
  icon: "camp"
  color: "SteelBlue"
  url: "/2025/05/refugio-grey"
  description: "Night 1. Book 6+ months ahead."

- name: "Grey Glacier viewpoint"
  lat: -50.9912
  lon: -73.1456
  icon: "summit"
  description: "Best views before noon."

- name: "Summit photo"
  lat: -50.9934
  lon: -73.1502
  icon: "photo"
  url: "https://example.com/uploads/2025/summit.jpg"
  description: "Click to view the photo."
```

If a waypoint's `url` points to an image file (`.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`), clicking it opens a lightbox overlay. When multiple waypoints on the same map have image URLs, arrows and swipe gestures let you browse all of them.

#### Waypoint field reference

| Field | Required | Description |
|---|---|---|
| `name` | Yes | The label displayed in the waypoint's popup. |
| `lat` | Yes | Latitude coordinate. |
| `lon` | Yes | Longitude coordinate. |
| `icon` | No | Icon shape. Defaults to `waypoint`. See [Available Icons](#available-icons). |
| `color` | No | Icon color. Defaults to the route line color. Accepts CSS color names or hex values. |
| `url` | No | Link destination. Image URLs open in a lightbox; all other URLs open in a new tab. |
| `description` | No | Short text shown below the name in the popup. |

## Available Icons

### Route icons

Purpose-built for outdoor and travel routes.

| Value | Description |
|---|---|
| `waypoint` | Open circle with center dot *(default)* |
| `start` | Filled circle with play triangle |
| `finish` | Filled circle with flag |
| `summit` | Filled circle with mountain outline |
| `camp` | Filled circle with tent |
| `shelter` | Filled circle with roof/hut |
| `water` | Filled circle with water drop |
| `viewpoint` | Filled circle with binoculars |
| `photo` | Filled circle with camera |
| `warning` | Filled circle with exclamation mark |
| `parking` | Filled circle with P |
| `restroom` | Filled circle with person |
| `food` | Filled circle with fork and knife |
| `lodging` | Filled circle with bed |
| `information` | Filled circle with i |
| `fuel` | Filled circle with fuel pump |
| `garage` | Filled circle with wrench |
| `gate` | Filled circle with barrier |
| `bridge` | Filled circle with arch |
| `tunnel` | Filled circle with portal |
| `ford` | Filled circle with water crossing |
| `launch` | Filled circle with boat |
| `portage` | Filled circle with carry figure |
| `rapids` | Filled circle with waves |
| `aid` | Filled circle with cross |
| `flag` | Filled circle with flag variant |
| `fork` | Filled circle with trail junction |
| `pass` | Filled circle with notched peak |
| `waterfall` | Filled circle with falling water |
| `beach` | Filled circle with wave |
| `cave` | Filled circle with arch opening |
| `wildlife` | Filled circle with paw print |
| `exposure` | Filled circle with cliff edge |
| `historic` | Filled circle with column |
| `natural` | Filled circle with leaf |
| `bike-repair` | Filled circle with wrench and wheel |
| `steep` | Filled circle with gradient arrow |
| `surface-change` | Filled circle with split surface |
| `seasonal` | Filled circle with snowflake/sun |

### Generic icon

| Value | Description |
|---|---|
| `pin` | Classic teardrop map pin with a white center dot |

### Numbered circles

Filled circles with a white number inside. Available in two series:

**Single digit:** `circle-0` through `circle-9`

**Two-digit (zero-padded):** `circle-00` through `circle-99`

```yaml
icon: "circle-3"
icon: "circle-12"
icon: "circle-99"
```

Use numbered circles for aid stations, checkpoints, campsites by night number, or any sequence you want to label.

### Emoji / Unicode

Any `icon` value that doesn't match a built-in name is rendered directly as an emoji or unicode character:

```yaml
icon: "⛺"
icon: "📸"
icon: "⚠️"
icon: "🏔️"
```

The `color` field has no effect on emoji icons. Appearance varies across operating systems and browsers.

## Base Maps

All base maps are free and require no API key.

```
{{< route source="w-trek-day1" basemap="topo" >}}
```

### General purpose

| Value | Description |
|---|---|
| `osm` | Standard OpenStreetMap tiles *(default)* |
| `voyager` | CARTO Voyager — clean, colorful, modern |
| `positron` | CARTO Positron — light gray, minimal |

### Outdoor & terrain

| Value | Description |
|---|---|
| `topo` | OpenTopoMap — contour lines and elevation shading, worldwide |
| `cycle` | CyclOSM — dedicated cycling map showing bike lanes, surface types, and elevation |
| `usgs-topo` | USGS Topo — official US Geological Survey topographic map. US only |
| `usgs-hybrid` | USGS Imagery Topo — satellite imagery with topo overlay. US only |
| `snow` | OpenSnowMap — pistes, lifts, and ski terrain |

### Satellite & imagery

| Value | Description |
|---|---|
| `satellite` | Esri World Imagery — satellite photography |
| `streets` | Esri World Street Map — detailed street map |
| `esri-topo` | Esri World Topo — polished topo with shaded relief |
| `esri-relief` | Esri Shaded Relief — clean hillshade, no labels. Good background for long routes |
| `natgeo` | National Geographic — warm parchment tones with shaded relief |

### Activity recommendations

| Activity | Suggested basemaps |
|---|---|
| Hiking | `topo`, `esri-topo`, `usgs-topo` |
| Cycling | `cycle`, `topo` |
| Running | `voyager`, `streets`, `osm` |
| Off-road | `satellite`, `usgs-hybrid`, `esri-relief` |
| Paddling | `topo`, `satellite`, `osm` |
| Scenic driving | `natgeo`, `esri-relief`, `voyager` |
| Skiing | `snow`, `topo` |

The Esri tiles use a legacy service that may require registration in the future; see [Esri's terms](https://wiki.openstreetmap.org/wiki/Esri) for details.

## Stats Bar

The stats bar appears automatically below the map in single-route mode, showing the following values parsed from the GPX file:

| Stat | Symbol | Notes |
|---|---|---|
| Distance | ↔ | Formatted as km or m |
| Elevation gain | ↑ | In meters |
| Elevation loss | ↓ | In meters |
| Moving time | — | Only shown if the GPX includes timestamps |

Set `stats="false"` in the shortcode or `stats: false` in the data file to hide it. The stats bar is never shown in multi-route mode — put condensed stats in each route's `description` field instead.

## Elevation Profile

The elevation profile is off by default. Enable it with `elevation="true"` in the shortcode or `elevation: true` in the data file:

```
{{< route source="w-trek-day1" elevation="true" >}}
```

When enabled, an SVG chart renders below the stats bar (or directly below the map if the stats bar is hidden). The chart shows the route's elevation curve as a filled area with a gradient, a stroke line, labeled Y-axis ticks, and start/end distance labels on the X axis.

The elevation profile requires GPX files that include elevation data (`<ele>` elements). Most GPS watches and fitness apps include this automatically. If elevation data is absent, the chart area remains empty and no error is shown.

## Start/Finish Markers

Start and finish markers are placed automatically at the endpoints of every route using the built-in `start` and `finish` icons in the route's line color. On loop routes where the start and finish are within ~30 meters of each other, only the start marker is placed.

Set `markers="false"` in the shortcode, `markers: false` in the data file, or per-route in a multi-route file to suppress them:

```yaml
- name: "Park boundary"
  gpx: "/uploads/2025/boundary.gpx"
  color: "#94a3b8"
  markers: false
```

## Route Legend

In multi-route mode, add a color-swatch legend below the map with `legend="true"`:

```
{{< route source="patagonia-hikes" legend="true" >}}
```

The legend lists each named route with a short horizontal swatch in its color. Routes with `popup: false` are excluded. If a route has a `legend_url` field (or a `url` field), its legend label becomes a clickable link.

```yaml
- name: "W Trek — Day 1"
  gpx: "/uploads/2025/w-trek-day1.gpx"
  color: "#E11D48"
  legend_url: "/2025/05/w-trek-day1"
```

## Route Simplification

GPX files record a coordinate point every 1–5 seconds. A full day of hiking can contain 10,000+ points, which can slow rendering on mobile — especially with multiple routes on one page.

Simplification uses the Ramer-Douglas-Peucker algorithm to reduce point count by 60–80% with no visible difference at typical map zoom levels.

```
{{< route source="w-trek-day1" simplify="true" >}}
```

Use a numeric value for finer control (tolerance in degrees):

```
{{< route source="w-trek-day1" simplify="0.00003" >}}
```

| Value | Approx. tolerance | Best for |
|---|---|---|
| `"true"` | ~5 m | General use |
| `"0.00003"` | ~3 m | Tight switchbacks |
| `"0.0001"` | ~11 m | Very large files |

Simplification is applied after the GPX loads and does not modify the source file.

## GPX Files

GPX files must be hosted on the same domain as your blog to avoid browser CORS restrictions. Upload them via **Uploads** in the Micro.blog navigation, then copy the URL to use in your data file.

GPX export is available in:
- **Strava** — Activity page → overflow menu → Export GPX
- **Garmin Connect** — Activity page → gear icon → Export to GPX
- **AllTrails** — Activity page → Export (Pro feature for recorded tracks; free for planned routes)
- **Komoot** — Tour page → Share → Download GPX
- **Apple Fitness / iPhone** — Requires a third-party app such as HealthFit to export

GPX files exported from GIS applications (ArcGIS, QGIS, Google Earth) also work. Tracks without timestamps will render correctly but the moving time stat will be omitted.

## Tips

- **Scroll zoom is disabled** so visitors don't get stuck in the map while scrolling. They can still zoom with the +/− buttons or by pinching on mobile.
- **Auto-fit:** The map automatically zooms and pans to fit the full route extent, with padding on all sides.
- **Finding waypoint coordinates:** Search for a location on [Google Maps](https://maps.google.com), right-click, and the coordinates appear at the top of the context menu. Click to copy.
- **Photo waypoints:** Set a waypoint's `url` to an image file hosted on your blog. The lightbox handles the rest — click the waypoint name to view full-screen, then swipe or use arrow keys to browse all image waypoints on the map.
- **Long routes:** Enable `simplify="true"` for routes over ~20 km, multi-day trips, or any map with multiple GPX files loading simultaneously.
- **Multi-route colors:** Each route should specify its own `color` field for clearest results. If colors are omitted, the auto-palette cycles through 10 distinct colors. Pass `color=` in the shortcode to force a single color across all routes.
- **Reference layers:** Use `popup: false` and `markers: false` together for routes that provide visual context (park boundaries, watershed outlines) without cluttering the map with interactive elements.
- **On Micro.blog, link to the `-m` (medium) version of images** in waypoint URLs to keep lightbox load times fast.

## Theme Compatibility

The plugin loads Leaflet's CSS and JavaScript on demand — no changes to your theme's `<head>` or footer are needed. It includes CSS overrides that prevent common theme styles (such as `max-width` or `border-radius` on images) from breaking Leaflet's tile rendering and marker positions.

If you use both Routes and Maps for Micro.blog on the same page, Leaflet loads only once and both plugins share the same lightbox instance and basemap definitions.

## Credits

- [Leaflet.js](https://leafletjs.com/) for the mapping library
- [leaflet-gpx](https://github.com/mpetazzoni/leaflet-gpx) by Maxime Petazzoni for GPX parsing
- [OpenStreetMap](https://www.openstreetmap.org/) contributors for tile data
- [CyclOSM](https://www.cyclosm.org/) for cycling tiles
- [OpenTopoMap](https://opentopomap.org/) for topographic tiles
- [OpenSnowMap](https://www.opensnowmap.org/) for ski tiles
- [Maps for Micro.blog](https://github.com/moonmehta/maps-for-microblog) for the shared architecture patterns

## License

MIT
