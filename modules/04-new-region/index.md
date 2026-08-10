---
title: "4 - Same Recipe, New Region"
---

# Same Recipe, New Region

We have built and explored a coastal DEM for **Newport, Oregon**.

For our final exercise, we will test one of the main ideas behind the Continuous DEMs framework:

> **Can we reuse the same workflow in a very different coastal region without starting over?**

We will move from Oregon to **Sarasota, Florida**.

:::{important}
## Short on time?

If we only have a few minutes left, focus on three things:

1. Identify what should change from Newport to Sarasota.
2. Find the local Sarasota topobathymetric lidar in NOAA Digital Coast DAV.
3. Launch the Sarasota build.

The Sarasota DEM does **not** need to finish during the workshop for this exercise to be useful.
:::

---

# 1. What should change?

Our Sarasota study area is:

```text
West:  -82.59
East:  -82.53
South:  27.28
North:  27.34
```

Before looking below, think about the Newport build.

Which parts of the recipe should stay the same?

Which parts should change?

:::{dropdown} Check your answer

**Mostly reusable**

```text
coupling-bathy-topo
-X 6:5
-P epsg:4269+5703
-E 0.1111111s
```

**Region-specific**

```text
-R bounds
local Digital Coast survey
output name
output directory
shared-cache location
```

The important separation is:

```text
reusable coastal workflow
        +
local data choices
        +
new study area
        ↓
new coastal DEM
```

:::

---

# 2. Find useful local data

Just as we did for Newport, we will start with the nationally available sources in:

```text
coupling-bathy-topo
```

and then look for a useful **local topobathymetric lidar dataset**.

Open NOAA Digital Coast DAV already focused on the Sarasota workshop area:

[Open NOAA Digital Coast DAV for Sarasota](https://coast.noaa.gov/dataviewer/#/lidar/search/-9198602.505474905,3155893.0057734232,-9186314.531330857,3155893.0057734232,-9186314.531330857,3166415.89275869,-9198602.505474905,3166415.89275869,-9198602.505474905,3155893.0057734232)

:::{important}
## Your turn: find the local topobathymetric lidar

Look at the lidar datasets intersecting this small area.

Find the dataset that:

1. overlaps our Sarasota study area
2. contains **topobathymetric lidar**
3. looks useful for improving a combined land-and-water DEM

Select the dataset to open its details.
:::

## Find the DAV dataset ID

The dataset details panel does not prominently display the numeric ID we need.

Instead:

1. Scroll down to **Bulk Download**.
2. Click **Link to All Dataset Files**.
3. A new page will open showing the files for that dataset.
4. Look at the URL in your browser.

Near the end of the URL, you will see a number like:

```text
.../10196/index.html
      ↑
  DAV dataset ID
```

That number is what we need.

:::{note}
DAV calls this the **dataset ID**.

Globato/Fetchez uses the same number with the `dav` source as:

```text
survey_id=10196
```
:::

Now complete:

```text
dav:survey_id=____,weight=100
```

:::{tip}
## You do not need to download the files

DAV is helping us **discover and inspect** the local dataset.

Once we know the dataset ID, Globato/Fetchez can access it through the `dav` source. You can close the Bulk Download page after identifying the number.
:::

:::{dropdown} Reveal the Sarasota dataset

The dataset is:

**2024 USACE FEMA Topobathy Lidar: Post Hurricane Milton, FL**

Its DAV dataset ID is:

```text
10196
```

So the source specification is:

```text
dav:survey_id=10196,weight=100
```

Compare that with Newport:

```text
Newport:   dav:survey_id=9693,weight=100
Sarasota:  dav:survey_id=10196,weight=100
```

The DAV/Globato pattern stays the same. Only the locally appropriate dataset ID changes.

:::

---

# 3. Build the Sarasota DEM

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
## Start the Sarasota build

This is the same basic workflow we used for Newport.

The main changes are the **region**, **local survey**, and **output/cache locations**.
:::

---

# 4. Compare Newport and Sarasota

While the Sarasota build starts, compare the two commands.

| Setting | Newport | Sarasota |
|---|---|---|
| Region | `-124.1/-124/44.59/44.64` | `-82.59/-82.53/27.28/27.34` |
| Standard bundle | `coupling-bathy-topo` | `coupling-bathy-topo` |
| Local DAV survey | `9693` | `10196` |
| Resolution | ~1/9 arc-second | ~1/9 arc-second |
| Reference system | NAD83 + NAVD88 | NAD83 + NAVD88 |
| Multiresolution settings | `-X 6:5` | `-X 6:5` |

:::{dropdown} What is the main lesson?

We did **not** create a new workflow for Florida.

We:

```text
changed the study area
        ↓
looked for useful local data
        ↓
added the local survey
        ↓
reused the same coastal recipe
```

That is the portability we want from the framework.

:::

---

# If time allows: watch the data discovery

Look at the Sarasota terminal output.

Can you identify:

- data discovery
- cached data being reused
- the Digital Coast lidar source
- transformations
- source preparation

Compare what you see with Newport.

> **The workflow can stay consistent even when the available source data change from place to place.**

---

:::{dropdown} If the Sarasota DEM finishes

Look inside:

```text
sarasota_dem/
```

Open the final DEM or hillshade and compare it briefly with Newport.

Look for obvious differences in:

- coastal morphology
- nearshore detail
- source-data coverage
- areas that require more interpolation

Do not try to perform a full evaluation here.

The purpose of this final example is to demonstrate that the **same recipe can be transferred to a new region and adapted with local data**.

:::

---

# The workflow we used today

Across the workshop, we followed a repeatable pattern:

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
        ↓
inspect the sources and processing
        ↓
evaluate with appropriate independent observations
        ↓
reuse the workflow somewhere else
```

---

# Final takeaway

The most important outcome of the workshop is not the Newport DEM or the Sarasota DEM.

It is the ability to start with a **reusable national-scale recipe**, add **local scientific knowledge**, and build a transparent workflow that can be applied to another coastal region.

:::{tip}
## Try your own study area

For another U.S. coastal region:

1. Define your bounds.
2. Start with `coupling-bathy-topo`.
3. Look in NOAA Digital Coast DAV for useful local lidar.
4. Add the appropriate survey when it improves the recipe.
5. Build, inspect, and evaluate the result.

That is the same pattern we used for both Newport and Sarasota.
:::

---

# Questions

If you applied this workflow to your own region:

> **What local dataset would you want to add first?**
