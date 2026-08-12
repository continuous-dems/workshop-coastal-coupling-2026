---
title: "5 - Evaluate the DEMs with IVERT"
subject: "Tutorial"
---

# 🛰️ Evaluate the DEMs with IVERT

:::{tip} 🧭 Where we are going
:icon: false

We have built two coastal DEMs, but we have not yet checked whether they are *right*.
In this module we will compare both surfaces against independent ICESat-2 satellite observations using IVERT.
Along the way we will see how an independent check can reveal bias and spatial patterns that a hillshade cannot.
:::

We now have:

- a completed **Newport, Oregon** DEM
- a **Sarasota, Florida** DEM building in the background
*(If you don't have these files, it's okay, we have backups for you! You can keep going.)*

By this point, we've:

- discovered and selected source elevation data
- transformed data into a common reference system
- filtered, prioritized, and gridded measurements
- interpolated the surface where measurements were sparse
- inspected the finished DEM and its spatial metadata
- transferred the same workflow to a second coastal region

Now we will ask an important final question:

> **How closely does a DEM agree with independent observations?**

For that, we use **IVERT:** the **ICESat-2 Validation of Elevations Reporting Tool**.

ICESat-2 is a NASA Satellite with an photon-counting green altimeter lidar called ATLAS. It scans the earth with 6 beams, repeats its orbits every 90 days, shoots 10,000 shots a second, and measures individual green photons coming back to the sensor.

:::{figure} ../../assets/images/icesat2_satellite.jpeg
:alt: Illustration of the ICESat-2 satellite in orbit, emitting six green laser beams arranged in three pairs that strike the surface below and trace six parallel ground tracks across the terrain.
:width: 60%
:align: center

**ICESat-2 and its six beams.** The ATLAS instrument splits its green laser into three pairs of beams, which trace six parallel ground tracks beneath the satellite. Elevations are measured only along those narrow tracks — not as a continuous grid — which is why IVERT compares a DEM cell-by-cell where tracks happen to cross it. *Image credit: NASA.*
:::

IVERT compares a finished DEM against ICESat-2 elevations and produces statistical and spatial views of the comparison. IVERT provides an **independent end-to-end check on the result of your DEM workflow**.

Conceptually:

```text
source discovery
      ↓
transformations
      ↓
filtering + gridding + interpolation
      ↓
source prioritization
      ↓
finished DEM
      +
IVERT: independent ICESat-2 observations
      ↓
independent check on the resulting surface
```

IVERT does *not* test every processing step. Instead, it asks whether the **combined result** agrees with independent observations where those observations are available.

:::{dropdown} Why not just inspect the hillshade?

Visual inspection is extremely useful for finding obvious artifacts, discontinuities, or unrealistic terrain.

But a DEM can look reasonable and still contain vertical bias or other elevation artifacts.

IVERT adds an independent quantitative check that can reveal:

- systematic vertical bias
- the overall magnitude and spread of elevation differences
- the existence of persistent outliers
- spatial patterns in the differences

Those patterns can help point us back toward source data or processing areas that deserve a closer look.
:::

---

Let's launch a new terminal so that we can explore IVERT while our Sarasota DEM continues building.
```text
File → New → Terminal
```

## 🔧 1. Point IVERT at the workshop database

Before validating anything, IVERT needs to know **where the independent observations live**.

Usually at home, you would **build** an IVERT database from NASA ICESat-2 data before running a validation. For simplicity in this workshop, a database of ICESat-2 granules has already been prepared and staged in your `~/workshop/ivert` directory, so we will point IVERT at that copy instead of its default location. Additionally, some pre-built conversion grids live in a cache directory we will also use.

### Use the pre-built database
In your new terminal, copy this command and run:
```bash
ivert options ivert_database_directory=~/workshop/ivert/granules cache_directory=~/workshop/ivert/cache --yes
```

| Option | Meaning |
|---|---|
| `ivert_database_directory=...` | Directory IVERT should read validation granules from |
| `cache_directory=...` | Directory where cached conversion grids have been pre-built |
| `--yes` | Accept the changes without an interactive confirmation |

:::{important} 🧪 Success check
:icon: false

The command should complete without an error and report that it has written your new options to ~/.ivert/user_config.ini

IVERT will now use the prepared workshop granules in `~/workshop/ivert` for every validation in this module.
:::

### What's in the database?

```bash
ivert database list
```

You should see roughly **41 granules**.

These are **subsetted ATL03 granules with classified photons**. Looking at the filenames tells us:

- they span **2020 through 2024**
- they cover **two geographic areas**

Those two areas are not a coincidence — they are the Newport, Oregon and Sarasota, Florida regions we've built DEMs for today.

:::{important} 🧪 Success check
:icon: false

`ivert database list` prints granule filenames. You can see about 41 of them.

If the list comes back empty, IVERT is probably still pointing at its default directory. Re-run the `ivert options` command above.
:::

---

## 🔧 2. Evaluate Newport first

While Sarasota continues building, we will run a Newport validation.

Newport gives us our first **independent check of the finished DEM**.

We created this surface from multiple elevation sources and several processing steps. IVERT lets us compare the result with ICESat-2 observations that were not used to construct the DEM surface.

If the DEM agrees well with those observations, that gives us independent evidence that the **combined workflow is producing reasonable elevations in the sampled area**.

If IVERT reveals systematic or spatially organized differences, that gives us a useful signal that something in the source data or processing chain may deserve closer inspection.

---

### Go to your Newport DEM

Navigate to the folder where your Newport DEM was written:

```bash
cd ~/workshop/newport_dem
```

:::{dropdown} Don't have a Newport DEM?

No problem — a ready-made backup DEM is staged in your workshop workspace. Copy it into your Newport output directory and run the validation there:

```bash
# Run this ONLY if you don't already have your own Newport DEM
mkdir -p ~/workshop/newport_dem && cp ~/workshop/ivert/example_dems/newport_n44x64_w124x10_final.tif ~/workshop/newport_dem && cd ~/workshop/newport_dem
```

:::

---

### Run the Newport validation

From your `~/workshop/newport_dem` directory, run:

```bash
ivert validate *_final.tif -n "Newport, OR"
```

| Option | Meaning |
|---|---|
| `*_final.tif` | DEM(s) to evaluate. In this case it matches any file in this folder ending in "_final.tif" (namely, your newport_n44x64_w124x10_final.tif file. It can validate multiple DEMs simultaneously that match such a pattern. |
| `-n "Newport, OR"` | Title for the DEM. This name will appear on the plot. |

IVERT compares the DEM elevations with the available independent ICESat-2 observations, and writes its outputs to an `ivert_results` sub-directory in that folder.

:::{warning} Watch your vertical datum

The DEMs we built today have well-formatted headers, so IVERT reads the vertical reference datum directly from the DEM and transforms the ICESat-2 data to match it. We don't have to do anything.

Not every DEM is so well behaved. Some do not clearly define their vertical datum in the header, and IVERT can then match the photons to the **wrong** datum — producing results that appear offset by meters, or even **dozens of meters**.

A validation that comes back with a large, suspiciously uniform bias is very often a datum problem, not a DEM problem.

If you are ever unsure, state the datum explicitly with the `-V`/`--vdatum` flag:

```bash
ivert validate *_final.tif -n "Newport, OR" --vdatum navd88
```

It accepts an EPSG code (`EPSG:5703`, `5703`) or a common short name (`navd88`, `egm2008`, `mllw`, …). Run `ivert validate --list-vdatums` to see every name IVERT recognizes.
:::

You should see five (5) files output in your "ivert_results" directory:

| File | Meaning |
|---|---|
| `newport_n44x64_w124x10_final_`**`results.h5`** | An HDF5 database of cell-level results. Generally unused for visual analysis, but useful for processing. All other results are different "views" of this file. |
| `newport_n44x64_w124x10_final_`**`plot.png`** | A plot showing a histogram of errors (including bathymetry results if they exist), and a 1:1 line, as well as the total RMSE at the top. |
| `newport_n44x64_w124x10_final_`**`errors.gpkg`** | A Point vector GeoPackage file of cell-level errors. Viewable in a GIS. |
| `newport_n44x64_w124x10_final_`**`errors.tif`** | A sparse GeoTiff of cell-level errors. Same as the GeoPackage, but in raster format. |
| `newport_n44x64_w124x10_final_`**`summary_stats.txt`** | A text file with high-level statistics from the validation. Useful for exporting to reports, etc. |


:::{important} 🧪 Success check
:icon: false

You should see IVERT begin processing the Newport DEM and the available ICESat-2 observations.

The result is an **independent check of the finished Newport surface**.
:::

---

### Look at the Newport results

In the **JupyterLab file browser** on the left side of the screen (the little folder icon):

1. Navigate into the `ivert_results` directory.
2. Open `newport_n44x64_w124x10_final_plot.png`.
3. If the image appears too large, press the `-` key to zoom out. It should look something like this:

:::{figure} ../../assets/images/newport_ivert_plot.png
:alt: IVERT validation plot for the Newport, Oregon DEM, showing a histogram of DEM-minus-ICESat-2 land elevation differences centered near zero and a one-to-one scatter plot of DEM against ICESat-2 elevations.
:width: 100%
:align: center

**Newport, Oregon IVERT results.** Panel A is the distribution of land elevation differences (0.04 ± 0.52 m); panel B compares DEM and ICESat-2 elevations directly. Overall RMSE is 0.526 m across 343 cells.
:::

4. Also open `newport_n44x64_w124x10_summary_stats.txt`.
5. Download the GeoPackage file `newport_n44x64_w124x10_final_errors.gpkg`. Right-click, download, and load into a local GIS (*Arc, QGIS, etc*) on top of your NewPort hillshade. You should be able to choose/inspect any point to see its values, and/or set the Display properties to a "Graduated" display on the "error" field to see the results. Here, for instance, is the Newport-errors Geopackage file in QGIS, atop the hillshade


:::{figure} ../../assets/images/newport_ivert_errors_qgis.jpg
:alt: QGIS window showing the Newport IVERT errors GeoPackage displayed over the Newport hillshade, with validation points symbolized by graduated elevation error values.
:width: 100%
:align: center

**Newport errors in QGIS.** The `_errors.gpkg` validation points are displayed over the Newport hillshade, with a graduated symbology on the `error` field.
:::

*(If you cannot view the GeoPackage file in your local GIS, skip this step or come back to it later.)*

Between the plot and the summary statistics, IVERT gives us:

- number of validation observations
- mean vertical bias
- RMSE
- standard deviation and error percentiles
- the distribution of elevation differences

:::{important} 🧪 Success check
:icon: false

You can see both the validation plot and the summary statistics for Newport.

Those two files are the independent evidence we will interpret next.
:::

---

### What can the Newport results tell us?

Look at the IVERT statistics.

Ask:

1. Is the DEM systematically high or low relative to ICESat-2?
2. Is the distribution of errors well-distributed (a bell curve), or is it bimodal or multi-modal?
3. How large are the elevation differences overall?
4. How much spread is there in those differences?
5. Are there a larger-than-expected number of outliers or "long tails"?

These results can reveal behavior that may not be obvious from the hillshade alone.

You can then start to explore the errors spatially.
The spatial metadata helps us investigate **what went into the DEM at those locations**.

That combination makes the workflow much more transparent and diagnosable.

---

### Connect Newport IVERT results back to spatial metadata

Return mentally to the spatial metadata from Module 3. Load it up in your GIS if it's available.

Choose an area sampled by IVERT and ask:

> **Which source data support the DEM at this location?**

This connects possible source-context to your errors, especially if errors happen to be particularly large in one specific portion of your DEM compared to others.

---

## 🔧 3. Return to Sarasota

Now check the terminal where the Sarasota build has been running.

First, see whether the final DEM has been created:

```bash
ls -lh ~/workshop/sarasota_dem/*_final.tif
```
:::{important} 🧪 Sarasota build check
:icon: false

If you see a Sarasota file ending in:

```text
_final.tif
```

the DEM is ready for IVERT.

If the build is still finishing, leave it running for another moment while we review the evaluation workflow.
Or if you'd rather not wait, "skip ahead" and use an already-completed Sarasota DEM for this segment (see *"Don't have a Sarasota DEM?"* below).
:::

Sarasota gives us an especially useful coastal DEM evaluation case because the available independent ICESat-2 observations include **bathymetric observations**.

That lets IVERT provide an independent check on part of the **submerged coastal surface**, in addition to the terrestrial portion of the DEM.

---

### Go to your Sarasota DEM

The database is already configured, so we can go straight to the DEM:

```bash
cd ~/workshop/sarasota_dem
```

:::{dropdown} Don't have a Sarasota DEM?

If your build has not finished — or you would rather not wait — use the backup DEM staged in your workshop workspace. We put it in a separate directory so it does not collide with the build still running:

```bash
# ONLY if you don't already have your own Sarasota DEM
mkdir -p ~/workshop/sarasota_dem_sample && cp ~/workshop/ivert/example_dems/sarasota_n27x34_w082x59_final.tif ~/workshop/sarasota_dem_sample && cd ~/workshop/sarasota_dem_sample
```

:::

---

### Run the Sarasota comparison

From your `~/workshop/sarasota_dem` or `~/workshop/sarasota_dem_sample` directory, run the Sarasota validation:

```bash
ivert validate *_final.tif -n "Sarasota, FL"
```

:::{important} 🧪 Success check
:icon: false

You should see IVERT begin processing the Sarasota DEM and the available independent ICESat-2 observations.

This gives us a second **independent end-to-end check of the DEM-building workflow**, now in a different coastal environment.
:::

---

### Look at the Sarasota results

As before, open the `ivert_results` directory and inspect:

```text
sarasota_n27x34_w082x59_final_plot.png
sarasota_n27x34_w082x59_summary_stats.txt
```

The plot should look something like this:

:::{figure} ../../assets/images/sarasota_ivert_plot.png
:alt: IVERT validation plot for the Sarasota, Florida DEM, showing separate histograms of land and bathymetric elevation differences alongside a one-to-one scatter plot of DEM against ICESat-2 elevations.
:width: 100%
:align: center

**Sarasota, Florida IVERT results.** Panel A is the land elevation differences (-0.03 ± 0.28 m), panel B the bathymetric differences (-0.01 ± 0.51 m), and panel C compares DEM and ICESat-2 elevations across both. Overall RMSE is 0.350 m across 1,027 cells. Note the separate bathymetric distribution — the terrestrial and submerged parts of the DEM are evaluated independently.
:::

Sarasota has a greater amount of shallow, non-turbid waters than Newport, OR, and ICESat-2 laser was able to survey some of the shallow seafloor in this DEM.

If you have time, as you did for Newport, download `sarasota_n27x34_w082x59_final_errors.gpkg` and load it into your local GIS on top of the Sarasota hillshade, with a "Graduated" display on the "error" field:

:::{figure} ../../assets/images/sarasota_ivert_errors_qgis.jpg
:alt: QGIS window showing the Sarasota IVERT errors GeoPackage displayed over the Sarasota hillshade, with validation points symbolized by graduated elevation error values over both land and shallow water.
:width: 100%
:align: center

**Sarasota errors in QGIS.** The `_errors.gpkg` validation points are displayed over the Sarasota hillshade, with a graduated symbology on the `error` field. Unlike Newport, many of these points fall **over water**, where ICESat-2 sampled the shallow seafloor — those cells are checking the bathymetric portion of the DEM.
:::

If anything does not work as expected, post a comment in the workshop chat and we will help you sort it out.

---

### What can the Sarasota results tell us?

Inspect the IVERT statistics and spatial outputs, similar to your Newport DEM.

Ask the same questions we asked for Newport:

1. Is there an overall vertical bias?
2. How large are the elevation differences?
3. What is the spread of those differences?
4. Are there outliers?
5. Are the differences spatially organized?

But Sarasota gives us an additional opportunity:

> **What do the independent bathymetric observations tell us about the submerged portion of the finished coastal DEM?**

This is particularly valuable for a topobathymetric workflow because we can independently check elevations **below the water surface**, not only the terrestrial terrain.

---

:::{important} The IVERT takeaway
:icon: false

IVERT gives us something we did not have during the DEM build:

> **an independent check on the result of the entire workflow.**

It can reveal bias, elevation differences, spatial patterns, and potential problems that may not be apparent from the DEM alone.

To interpret those results responsibly, we also consider:

1. where the independent observations are located
2. what part of the DEM they sample
3. whether they are independent of the DEM inputs (*Note*: you should probably not use ICESat-2 to validate a DEM if you used any ICESat-2 data in the DEM itself. Avoid circular logic!)
4. how the observed differences relate back to the DEM's source data and processing

:::

---

## Extensions

These virtual machines persist about ~1 week after the workshop. You can re-login and continue your work during that time, or better yet, install the `globato` and `ivert` packages on your own machine to use them permanently!

Here are some "extra tasks" to try in the cloud (or on your own machine), if you have want to explore more.

:::{tip} EXTRA: Try your own study area
:icon: false

For another U.S. coastal region:

1. Define your bounds.
2. Start with `coupling-bathy-topo`.
3. Search NOAA Digital Coast DAV for useful local lidar.
4. Add the appropriate dataset when it improves the recipe.
5. Build the DEM.
6. Inspect the finished surface.
7. Inspect where the elevations came from.
8. Evaluate the finished DEM with appropriate independent observations.
9. Interpret the evaluation together with its spatial coverage.

That is the same workflow we used for Newport and Sarasota.
:::

:::{tip} EXTRA: Build your own IVERT database
:icon: false
In addition to validating a DEM, IVERT can be used to **build a database of classified ICESat-2 photons** for your own DEM validations, or for other purposes entirely.

This process is often lengthy (min 20 minutes, >1 hour for larger queries) and not covered directly here, but if you want to try it yourself, see [Building your own IVERT database](./building-an-ivert-database.md). Try it out yourself after this tutorial is over.
:::

## Before you leave

There are a few final steps we'd like you to take before logging off of your virtual machine. Please read **[6 - Wrap it up](../06-wrap-it-up/index.md)** before closing your browser.
