---
title: "4 - Same Recipe, New Region"
---

# Same Recipe, New Region

We built and explored a coastal DEM for **Newport, Oregon**.

Now we will test whether the same workflow can be reused in a very different coastal setting:

> **Sarasota, Florida**

The goal is not to build a completely new workflow.

The goal is to decide:

```text
what stays the same?
        +
what changes locally?
        ↓
new coastal DEM
```

We will adapt the Newport recipe, launch the Sarasota build, and then **leave it running** while we move on to IVERT in Module 5.

---

# 1. Move to Sarasota

Our Sarasota study area is:

```text
West:  -82.59
East:  -82.53
South:  27.28
North:  27.34
```

<!-- IMAGE PLACEHOLDER
High-value figure:

Map of the Sarasota workshop AOI.

Ideally show:
- Sarasota coastline
- workshop bounding box
- enough regional context to orient participants

Suggested caption:
"Sarasota workshop study area on the west coast of Florida."
-->

Compare this with Newport:

```text
Newport, Oregon
        ↓
rocky Pacific coast + Yaquina Bay

Sarasota, Florida
        ↓
low-relief Gulf coast + shallow nearshore waters
```

The coastal setting changes substantially.

The workflow should not have to start over.

---

# 2. What should stay the same?

Before looking at the answer, think back to the Newport command.

Which parts should probably remain reusable?

Which parts should change for Sarasota?

:::{important}
## Your turn

Classify these as either:

**mostly reusable**

or

**region-specific**

```text
coupling-bathy-topo
-R bounds
-P epsg:4269+5703
-X 6:5
-E 0.1111111s
local DAV dataset
output name
output directory
shared-cache location
```
:::

:::{dropdown} Check your answer

**Mostly reusable**

```text
coupling-bathy-topo
-P epsg:4269+5703
-X 6:5
-E 0.1111111s
```

**Region-specific**

```text
-R bounds
local DAV dataset
output name
output directory
shared-cache location
```

That separation is central to the framework:

```text
reusable coastal recipe
        +
local choices
        ↓
region-specific DEM
```

Here is the full Newport-to-Sarasota comparison:

| Setting | Newport | Sarasota |
|---|---|---|
| Region | `-124.10/-124.00/44.59/44.64` | `-82.59/-82.53/27.28/27.34` |
| National bundle | `coupling-bathy-topo` | `coupling-bathy-topo` |
| Local DAV dataset | `9693` | `10196` |
| Output directory | `newport_dem` | `sarasota_dem` |
| Shared cache | `shared/newport_data` | `shared/sarasota_data` |
| Resolution | ~1/9 arc-second | ~1/9 arc-second |
| Reference system | NAD83 + NAVD88 | NAD83 + NAVD88 |
| Tile overlap / source-data buffer | `-X 6:5` | `-X 6:5` |

:::

<!-- IMAGE PLACEHOLDER
Suggested visual:

A simple Newport → Sarasota transfer diagram.

Example:

Newport
[AOI + DAV 9693]
        \
         > coupling-bathy-topo + common settings
        /
Sarasota
[AOI + DAV ????]

Suggested caption:
"Most of the workflow is reusable; the study area and locally appropriate data change."
-->

---

# 3. Find useful local Sarasota data

Just as we did for Newport, we will start from:

```text
coupling-bathy-topo
```

and then look for a locally useful **topobathymetric lidar dataset**.

Open NOAA Digital Coast DAV already focused on the Sarasota workshop area:

[Open NOAA Digital Coast DAV for Sarasota](https://coast.noaa.gov/dataviewer/#/lidar/search/-9198602.505474905,3155893.0057734232,-9186314.531330857,3155893.0057734232,-9186314.531330857,3166415.89275869,-9198602.505474905,3166415.89275869,-9198602.505474905,3155893.0057734232)

<!-- IMAGE PLACEHOLDER
Suggested figure:
DAV screenshot centered on the Sarasota AOI with lidar results/footprints visible.

Suggested caption:
"NOAA Digital Coast DAV lets us search for local lidar that overlaps the Sarasota study area."
-->

:::{important}
## Your turn: choose the local dataset

Look at the lidar datasets intersecting this small area.

Find the dataset that:

1. overlaps the Sarasota study area
2. contains **topobathymetric lidar**
3. would be useful for a combined land-and-water DEM

Select it to open the dataset details.
:::

---

# 4. Find the DAV dataset ID

Use the same method we used in Newport.

In the dataset details:

1. Scroll to **Bulk Download**.
2. Click **Link to All Dataset Files**.
3. A new page will open showing the dataset files.
4. Look at the URL in your browser.

<!-- IMAGE PLACEHOLDER
Very high-value figure:

Screenshot of:
Bulk Download
    ↓
Link to All Dataset Files

Reuse the same visual style as the Newport instructions if possible.

Suggested caption:
"Use Bulk Download → Link to All Dataset Files to find the DAV dataset ID."
-->

Near the end of the URL, you will see:

```text
.../XXXXX/index.html
      ↑
  DAV dataset ID
```

DAV calls this the **dataset ID**.

Globato/Fetchez uses that same number as:

```text
survey_id=XXXXX
```

Try completing:

```text
dav:survey_id=____,weight=100
```

:::{dropdown} Reveal the Sarasota dataset

The dataset is:

**2024 USACE FEMA Topobathy Lidar: Post Hurricane Milton, FL**

Its DAV dataset ID is:

```text
10196
```

So the Globato source specification is:

```text
dav:survey_id=10196,weight=100
```

Compare that with Newport:

```text
Newport:   dav:survey_id=9693,weight=100
Sarasota:  dav:survey_id=10196,weight=100
```

The source syntax stays the same.

Only the locally appropriate dataset changes.

:::

:::{tip}
## You do not need to download the files from DAV

DAV is helping us **discover and inspect** the local dataset.

Once we know the ID, Globato/Fetchez can access it through the `dav` source.
:::

:::{dropdown} Short on time?

If the group is ready to move on, use:

```text
dav:survey_id=10196,weight=100
```

and continue.

The point of the exercise is understanding how the local dataset enters the reusable recipe.
:::

---

# 5. Edit the Newport recipe

Before revealing the complete Sarasota command, look at the Newport command and decide what you would change.

Newport used:

```bash
globato build \
  -R -124.10/-124.00/44.59/44.64 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E 0.1111111s \
  -O newport \
  -D newport_dem \
  --shared-cache shared/newport_data \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

:::{important}
## Your turn: change only what is necessary

For Sarasota, update:

- the region
- the local DAV dataset
- the output name
- the output directory
- the cache location

Leave the reusable parts alone.
:::

:::{dropdown} Reveal the Sarasota command

```bash
globato build \
  -R -82.59/-82.53/27.28/27.34 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E 0.1111111s \
  -O sarasota \
  -D sarasota_dem \
  --shared-cache shared/sarasota_data \
  coupling-bathy-topo \
  dav:survey_id=10196,weight=100
```

:::

---

# 6. Launch the Sarasota build

Run:

```bash
globato build \
  -R -82.59/-82.53/27.28/27.34 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E 0.1111111s \
  -O sarasota \
  -D sarasota_dem \
  --shared-cache shared/sarasota_data \
  coupling-bathy-topo \
  dav:survey_id=10196,weight=100
```

:::{important}
## Success check

Once the Sarasota command is running successfully:

- Globato has started the workflow
- cached or source data are being discovered or reused
- the Sarasota DAV source appears in the workflow

**Leave this terminal running.**

Do not wait here for the DEM to finish.

We will introduce IVERT and evaluate Newport while Sarasota continues building, then return to Sarasota later in Module 5.
:::

<!-- TERMINAL SCREENSHOT PLACEHOLDER
Optional:
Short screenshot showing a healthy Sarasota build beginning.

Suggested caption:
"The Sarasota build starts with the same workflow structure used for Newport."
-->

---

# Next: evaluate the DEMs

Sarasota is now building in the background.

Next:

> **Module 5 — Evaluate the DEMs with IVERT**

We will:

```text
introduce IVERT
      ↓
evaluate Newport
      ↓
return to Sarasota
      ↓
evaluate Sarasota
```

:::{tip}
Keep the Sarasota build terminal open.

We will come back to it after the Newport IVERT exercise.
:::
