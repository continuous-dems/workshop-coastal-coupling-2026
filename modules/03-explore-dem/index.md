---
title: "3 - Explore the Newport DEM"
---

# Explore the Newport DEM

We now have a coastal DEM for **Newport, Oregon**.

In this module, we will move from **building** the DEM to **understanding** it.

Our workflow is:

```text
inspect the DEM
      ↓
inspect the spatial metadata
      ↓
connect the finished surface to its source data
      ↓
recognize where measurements directly support the DEM
and where interpolation plays a larger role
```

The key question is:

> **What source data support this part of the DEM?**

---

# 1. Check the Newport outputs

The two primary workshop products are:

```text
newport_dem/newport_n44x64_w124x10_final.tif
newport_dem/newport_n44x64_w124x10_hs.tif
```

If your build has finished, run:

```bash
ls -lh newport_dem/newport_n44x64_w124x10_final.tif newport_dem/newport_n44x64_w124x10_hs.tif
```

:::{important}
## Success check

You should see both files listed:

```text
newport_dem/newport_n44x64_w124x10_final.tif
newport_dem/newport_n44x64_w124x10_hs.tif
```

If your build did not finish, use the instructor-provided Newport outputs and continue with the group.
:::

<!-- TODO / RESCUE PATH PLACEHOLDER
Add the exact shared path for instructor-provided Newport outputs.

Suggested wording:

"If your build is still running or failed, use:

[EXACT SHARED NEWPORT OUTPUT PATH]

for the remainder of Module 3."
-->

---

# 2. Look at the finished DEM

Open:

```text
newport_dem/newport_n44x64_w124x10_final.tif
newport_dem/newport_n44x64_w124x10_hs.tif
```

Use the hillshade first to get oriented.

Look at:

- Yaquina Bay
- the entrance channel
- the coastline
- adjacent terrestrial terrain
- nearshore bathymetry
- transitions between detailed and smoother areas

<!-- IMAGE PLACEHOLDER
Highest-value visual for this section:

Side-by-side:
1. Newport DEM
2. Newport hillshade

Ideally use the same map extent and include a small locator or coastline outline.

Suggested caption:
"The finished Newport coastal DEM and hillshade."
-->

:::{important}
## Your turn: pick one place

Choose one feature or area that catches your attention.

For example:

- a detailed topographic feature
- the shoreline
- the entrance channel
- an area of smooth bathymetry
- a transition between different-looking parts of the DEM

Keep that location in mind.

We will try to determine **what source data support it**.
:::

---

# 3. Where did those elevations come from?

The hillshade shows us the shape of the finished surface.

The **spatial metadata** helps us understand where the elevation data came from.

Globato writes a spatial-metadata GeoPackage alongside the Newport DEM outputs.

For this workshop build, the file is:

```text
newport_dem/newport_n44x64_w124x10_sm.gpkg
```

Confirm that it was created:

```bash
ls -lh newport_dem/newport_n44x64_w124x10_sm.gpkg
```

:::{important}
## Success check

You should see:

```text
newport_dem/newport_n44x64_w124x10_sm.gpkg
```

This is one of the most useful supporting products from the build.
:::

---

## View the spatial metadata with the hillshade

The easiest way to interpret the spatial metadata is to view it **on top of the Newport hillshade**. This lets you compare the finished DEM surface with the source-data coverage behind it.

For this exercise, use the desktop GIS application you are most comfortable with, such as **QGIS, ArcGIS Pro, or another GIS**.

### Download the files

In the **JupyterLab file browser** on the left side of the screen:

1. Open the `newport_dem` folder.
2. Find the file you want to download.
3. **Right-click the file.**
4. Select **Download**.

:::{tip} Files to download

Download these two files:

**Hillshade**

```text
newport_dem/newport_n44x64_w124x10_hs.tif
```

**Spatial metadata**

```text
newport_dem/newport_n44x64_w124x10_sm.gpkg
```

Open both files in your GIS and display the **spatial metadata on top of the hillshade**.
:::

:::{note}
## Don't have a desktop GIS available?

No problem.

Follow along as the instructor shares their screen and explores the Newport hillshade and spatial metadata.

The important part of this exercise is understanding **what the spatial metadata tells us about the source data behind the DEM**, not learning a particular GIS application.
:::

<!-- IMAGE PLACEHOLDER
HIGHEST-VALUE FIGURE FOR THIS SECTION:

A Newport hillshade with the spatial-metadata GeoPackage overlaid.

Ideally:
- use the Newport hillshade as a grayscale background
- show the spatial metadata with transparent fills or outlines
- distinguish source datasets clearly
- include a concise legend
- preserve enough hillshade visibility to see Yaquina Bay, the entrance
  channel, coastline, and surrounding topography

Suggested caption:
"Newport hillshade with Globato spatial metadata showing where source elevation datasets contribute to the DEM."
-->
The spatial metadata lets us connect the finished surface back to the data used to build it.

Instead of asking only:

> **What does the DEM look like here?**

we can also ask:

> **What source data support this part of the DEM?**

---

## Your turn: connect the DEM to the source data

Return to the location you picked in the previous section.

Using the hillshade and spatial metadata together, ask:

1. Which source dataset covers this location?
2. Is there more than one source nearby?
3. Does the hillshade look especially detailed where dense measurements are available?
4. Where do the spatial metadata become sparse or absent?
5. Where might interpolation therefore play a larger role?

:::{tip}
## Spatial metadata and interpolation

The spatial metadata identifies where source elevation data contribute to the DEM.

Areas without direct source-data coverage are especially important to recognize because the final surface there depends more heavily on interpolation between available measurements.

The hillshade shows the **surface**.

The spatial metadata shows the **measurement support behind that surface**.
:::

---

## Inspect the metadata attributes

The GeoPackage also contains attributes describing the source-data features.

<!-- TODO / CODE PLACEHOLDER
Once the final spatial-metadata schema is confirmed, add one simple command
or short Python snippet that prints only the useful columns.

For example, show:
- source/provider
- dataset or survey identifier
- weight/priority if present
- any field that distinguishes source-data support from interpolation

Do not expose a long table of implementation-specific fields.
-->

:::{important}
## One-location challenge

Pick one location in the Newport DEM and be able to describe it in one sentence:

> **This part of the DEM is supported by __________, and the surrounding surface appears __________.**

The purpose is not to catalog every source.

It is to learn how to use the spatial metadata to interpret the finished DEM.
:::

---

---

# What have we learned?

At this point, we have:

- inspected the finished Newport DEM and hillshade
- used the spatial metadata to connect the finished surface back to its source data
- identified where direct source-data support is dense, sparse, or absent
- considered where interpolation therefore plays a larger role
- practiced tracing one location in the DEM back to the measurements that support it

The key distinction is:

```text
Hillshade:
What does the finished surface look like?

Spatial metadata:
What source data support that surface?
```

:::{important}
## The takeaway

A DEM is more useful when we can understand not only **what the surface looks like**, but also **what measurements support it**.

The spatial metadata gives us that connection.
:::

---

# Next: apply the workflow somewhere new

We have built and explored Newport.

Next:

> **Module 4 — Same Recipe, New Region**

We will move to **Sarasota, Florida**, identify locally appropriate topobathymetric lidar, adapt the Newport recipe, and launch a second DEM build.

We will then leave Sarasota running while we introduce **IVERT** and evaluate both DEMs in **Module 5 — Evaluate the DEMs with IVERT**.
