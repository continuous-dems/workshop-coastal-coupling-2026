---
title: "2 - Build the Newport DEM"
---

# Build the Newport DEM

We will start the Newport DEM early so it can run while we discuss how the workflow works.

```text
define the study area
        ↓
start with a national coastal data recipe
        ↓
look for useful local data
        ↓
add local data to the recipe
        ↓
build the coastal DEM
```

---

# Newport study area

Our main example is **Newport, Oregon**, including Yaquina Bay, the entrance channel, nearby coastal waters, and adjacent topography.

```text
West:  -124.10
East:  -124.00
South:   44.59
North:   44.64
```

We will build an approximately **1/9-arc-second** coastal DEM referenced to:

```text
Horizontal: NAD83
Vertical:   NAVD88
```

represented by:

```text
epsg:4269+5703
```

Our reusable starting recipe is:

```text
coupling-bathy-topo
```

---

# Start with nationally available data

The `coupling-bathy-topo` bundle brings together several strong **national-scale coastal elevation data sources** that are useful starting points for building a DEM anywhere in the United States.

Depending on the location, the bundle may include sources such as:

| Source | What it contributes |
|---|---|
| **USGS The National Map (TNM)** | Topographic elevation data, including lidar-derived products |
| **NOAA hydrographic surveys** | Modern measured bathymetry, including BAG and point-based survey data |
| **USACE eHydro** | Hydrographic survey data from maintained navigation channels and waterways |
| **NOAA nautical charts** | Supplemental chart-derived bathymetric information |
| **Crowdsourced bathymetry (CSB)** | Additional depth observations where available |

:::{note}
The exact sources and options in the bundle may evolve, and not every source has useful coverage in every study area.
:::

These national-scale datasets give us a strong, reusable baseline:

```text
national-scale data sources
        ↓
standard coastal DEM recipe
```

But coastal elevation data are often especially rich at the **local and regional scale**.

A state, federal program, or recent project may have collected newer or higher-resolution lidar, sonar, or topobathymetric data that are not part of the standard bundle.

So a good workflow is:

```text
start with strong national-scale sources
        ↓
look for better local data
        ↓
add it when it improves the recipe
```

For Newport, NOAA Digital Coast gives us exactly that opportunity.

---

# Find the Newport topobathymetric lidar

Open NOAA's **Digital Coast Data Access Viewer (DAV)** already focused on our Newport study area:

[Open NOAA Digital Coast DAV for Newport](https://coast.noaa.gov/dataviewer/#/lidar/search/-13815132.111459369,5557767.363023302,-13802740.194591334,5557767.363023302,-13802740.194591334,5565765.68390708,-13815132.111459369,5565765.68390708,-13815132.111459369,5557767.363023302)

:::{important}
## Find the survey

Look at the lidar datasets intersecting this small area.

Your goal is simple:

> **Find the topobathymetric lidar survey that you would add to the Newport coastal DEM.**

When you find it, note its **Digital Coast survey ID**.
:::

Use the ID you found to complete:

```text
dav:survey_id=____,weight=100
```

:::{dropdown} Reveal the survey

The Newport topobathymetric lidar is Digital Coast survey:

```text
9693
```

So the Globato source specification is:

```text
dav:survey_id=9693,weight=100
```

:::

This is how local data can be added to a reusable recipe:

```text
coupling-bathy-topo
        +
local Newport lidar
```

---

# Start the Newport build

Run:

```bash
globato build \
  -R -124.1/-124/44.59/44.64 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E 0.1111111s \
  -O newport \
  -D newport_cudem \
  --shared-cache coupling-shared-dir \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

:::{important}
## Start this command now

Leave the build running.

Everything below can be discussed while Globato is processing the DEM.
:::

---

# Can you read the command?

Before looking at the answers, find these four things in the command:

1. **Where** are we building the DEM?
2. **Which local lidar survey** did we add?
3. **What resolution** are we requesting?
4. **What horizontal + vertical reference system** are we using?

:::{dropdown} Check your answers

| Question | Answer |
|---|---|
| Study area | `-R -124.1/-124/44.59/44.64` |
| Local lidar | `dav:survey_id=9693,weight=100` |
| Resolution | `-E 0.1111111s` |
| Reference system | `-P epsg:4269+5703` = NAD83 + NAVD88 |

Other important pieces are:

| Part | Purpose |
|---|---|
| `-X 6:5` | Multiresolution DEM settings |
| `-O newport` | Output name |
| `-D newport_cudem` | Processing/output directory |
| `--shared-cache coupling-shared-dir` | Shared workshop source-data cache |
| `coupling-bathy-topo` | Standard coastal recipe |

:::

---

# What would change in another region?

Now that the Newport build is running, consider what you would need to change if we moved somewhere else.

:::{dropdown} Reusable or region-specific?

**Mostly reusable**

```text
coupling-bathy-topo
-P epsg:4269+5703
-X 6:5
```

**Region-specific**

```text
-R bounds
local DAV survey
output name
output directory
```

The key idea is:

```text
reusable coastal recipe
        +
local data choices
        +
new study area
        ↓
new coastal DEM
```

We will test exactly this idea later with **Sarasota, Florida**.

:::

---

# Continue while the DEM runs

Go directly to **Module 2b — Follow the Newport Build**.

Keep the terminal visible if possible. In Module 2b, we will connect the messages appearing in the terminal to the major stages of the workflow.
