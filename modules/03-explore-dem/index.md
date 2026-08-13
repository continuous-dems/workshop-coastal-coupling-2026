---
title: "3 - Explore the Newport DEM"
---

# Explore the Newport DEM

We now have a finished coastal DEM for **Newport, Oregon**.

The key question in this module is:

> **What source data support this part of the DEM?**

We will answer that by viewing the **hillshade** together with the **spatial metadata**.

---

# 1. Confirm the Newport files

You need these files:

```text
~/workshop/newport_dem/newport_n44x64_w124x10_hs.tif
~/workshop/newport_dem/newport_n44x64_w124x10_sm.gpkg
```

If you are using QGIS, also use:

```text
~/workshop/newport_dem/newport_n44x64_w124x10_sm.qml
```

Confirm the files are present:

```bash
ls -lh \
  ~/workshop/newport_dem/newport_n44x64_w124x10_hs.tif \
  ~/workshop/newport_dem/newport_n44x64_w124x10_sm.gpkg
```

:::{dropdown} If your Newport build did not finish

Known-good Newport files are available in:

```text
~/workshop/reference_outputs/newport/
```

If needed, copy them into the Newport output directory:

```bash
rm -rf ~/workshop/newport_dem
mkdir -p ~/workshop/newport_dem
cp -a ~/workshop/reference_outputs/newport/. ~/workshop/newport_dem/
cd ~/workshop/newport_dem
ls -lh
```

Then rerun the `ls` command above.
:::

---

# 2. Look at the finished surface

Open the Newport hillshade:

```text
~/workshop/newport_dem/newport_n44x64_w124x10_hs.tif
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

Open:

```text
~/workshop/newport_dem/newport_n44x64_w124x10_sm.gpkg
```

and display it **on top of the Newport hillshade**.

:::{figure} ../../assets/images/newport_sm.png
:alt: Newport, Oregon hillshade with spatial metadata overlaid, showing the source elevation datasets contributing to different parts of the DEM.
:width: 100%
:align: center

**Newport hillshade with spatial metadata.** The overlay connects places in the finished DEM with the source elevation data used to build it.
:::

:::{dropdown} How to download the files from JupyterLab

In the JupyterLab file browser:

1. Open `workshop`, then `newport_dem`.
2. Right-click the file.
3. Select **Download**.

Download:

```text
newport_n44x64_w124x10_hs.tif
newport_n44x64_w124x10_sm.gpkg
```

If you use QGIS, also download:

```text
newport_n44x64_w124x10_sm.qml
```

Keep the `.gpkg` and `.qml` files together. If QGIS does not apply the prepared style automatically, load the `.qml` as the layer style.
:::

:::{note}
## No desktop GIS?

Follow along with the instructor.

The goal is to interpret the DEM and its source support—not to learn a particular GIS application.
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
