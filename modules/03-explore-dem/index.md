---
title: "3 - Explore the Newport DEM"
---

# Explore the Newport DEM

We now have a finished coastal DEM for **Newport, Oregon**.

The key question in this module is:

> **What source data support different areas of the DEM?**

We will answer that by viewing the **hillshade** together with the **spatial metadata** in a desktop GIS such as **QGIS** or **ArcGIS Pro**.

:::{note}
## No desktop GIS?

Just follow along with the instructor!

The goal is to interpret the DEM and its source support—not to learn a particular GIS application.
:::

---

# 1. Get the Newport files into your GIS

First, confirm that the files are available:

```bash
ls -lh \
  ~/workshop/newport_dem/newport_n44x64_w124x10_hs.tif \
  ~/workshop/newport_dem/newport_n44x64_w124x10_sm.gpkg
```

If you are using **QGIS**, also use the prepared style file:

```text
newport_n44x64_w124x10_sm.qml
```

## Download the files from JupyterLab

In the JupyterLab file browser:

1. Open `workshop` folder on the left side, then `newport_dem`.
2. Right-click each file.
3. Select **Download**.

Download:

```text
newport_n44x64_w124x10_hs.tif
newport_n44x64_w124x10_sm.gpkg
```

For QGIS, also download:

```text
newport_n44x64_w124x10_sm.qml
```

Keep the `.gpkg` and `.qml` files together.

:::{tip}
If QGIS does not apply the prepared style automatically, load `newport_n44x64_w124x10_sm.qml` as the layer style.
:::

:::{dropdown} If your Newport build did not finish

If the Newport build is still running, first stop it with **Ctrl+C**.

Known-good Newport files are available in:

```text
~/workshop/reference_outputs/newport/
```

Copy them into the Newport output directory:

```bash
mkdir -p ~/workshop/newport_dem
cp -a ~/workshop/reference_outputs/newport/. ~/workshop/newport_dem/
```

Then download the files from JupyterLab.
:::

---

# 2. Look at the finished surface

Open the Newport hillshade in your GIS:

```text
newport_n44x64_w124x10_hs.tif
```

:::{figure} ../../assets/images/newport_hs.png
:alt: Hillshade of the finished Newport, Oregon coastal DEM around Yaquina Bay.
:width: 100%
:align: center

**Newport coastal DEM hillshade.** The hillshade reveals the shape and texture of the finished elevation surface around Yaquina Bay.
:::

Look for:

- Yaquina Bay
- the entrance channel
- the coastline
- adjacent terrestrial terrain
- nearshore bathymetry
- transitions between detailed and smoother areas

:::{important}
## Pick one place

Choose **one location** that catches your attention.

Keep it in mind. Next we will determine what source data support that part of the DEM.
:::

---

# 3. Add the spatial metadata

The hillshade shows the **finished surface**.

The spatial metadata shows the **source-data support behind that surface**.

Add:

```text
newport_n44x64_w124x10_sm.gpkg
```

to your GIS and display it **on top of the Newport hillshade**.

:::{figure} ../../assets/images/newport_sm.png
:alt: Newport, Oregon hillshade with spatial metadata overlaid, showing the source elevation datasets contributing to different parts of the DEM.
:width: 100%
:align: center

**Newport hillshade with spatial metadata.** The overlay connects places in the finished DEM with the source elevation data used to build it.
:::

---

# 4. Trace one location back to its source

Return to the location you selected.

Using the hillshade and spatial metadata together, ask:

1. **Which source dataset supports this location?**
2. **Is direct source-data coverage dense, sparse, or absent nearby?**
3. **Where might interpolation therefore play a larger role?**

:::{important}
## One-location challenge

Be able to describe your location in one sentence:

> **This part of the DEM is supported by __________, and the surrounding surface appears __________.**
:::

:::{dropdown} Inspect the spatial-metadata attributes

The GeoPackage includes attributes that provide additional information about the mapped source-data features, including the source module and relative weight or priority.

:::{figure} ../../assets/images/newport_sm_attributes.png
:alt: Newport hillshade with the spatial-metadata attribute table open, showing source-data information associated with mapped areas of the DEM.
:width: 100%
:align: center

**Spatial-metadata attributes.** The attribute table provides additional information about the source data supporting each mapped area.
:::

---

# The takeaway

```text
Hillshade:
What does the finished surface look like?

Spatial metadata:
What source data support that surface?
```

A DEM is more useful when we can understand both the **surface** and the **measurements behind it**.

---

# Next: apply the workflow somewhere new

Next:

> **Module 4 — Same Recipe, New Region**

We will move to **Sarasota, Florida**, change the local pieces of the Newport recipe, and launch a second DEM build.
