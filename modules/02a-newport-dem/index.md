---
title: "2 - Build the Newport DEM"
---

# Build the Newport DEM

Now we will build our main workshop DEM for **Newport, Oregon**.

The recipe is simple:

```text
reusable national coastal data
            +
local Newport topobathymetric lidar
            ↓
      Newport coastal DEM
```

We will start the build here and let it run while we move into **Module 2b — Inside the Workflow**.

---

# 1. Newport study area

Our study area includes **Yaquina Bay**, the entrance channel, nearby coastal waters, and adjacent topography.

:::{figure} ../../assets/images/newport_aoi.png
:alt: Satellite and aerial imagery of the Newport, Oregon workshop study area around Yaquina Bay, with the final DEM region outlined in red.
:width: 100%
:align: center

**Newport, Oregon Study Area.** The red box shows the region used to build the workshop DEM.
:::

For this workshop, we will build an approximately **1/9-arc-second** DEM using:

- **Bounds:** `-124.10/-124.00/44.59/44.64`
- **Horizontal datum:** NAD83
- **Vertical datum:** NAVD88
- **Reference system:** `epsg:4269+5703`

:::{tip}
You do not need to memorize the coordinates or EPSG codes.

The important idea is that a DEM build needs a **study area**, **resolution**, and **target reference system**.
:::

---

# 2. Start with a national recipe, then add local data

Our reusable national starting recipe is:

```text
coupling-bathy-topo
```

It combines nationally available coastal elevation sources into a starting point that we can improve with appropriate local data.

For Newport, our question is:

> **Is there a useful local dataset that would improve the national recipe?**

:::{dropdown} What is in `coupling-bathy-topo`?

To inspect the bundle, run:

```bash
fetchez modules bundles info coupling-bathy-topo
```

The bundle brings together several types of nationally available topographic and bathymetric data. Exact contents and options may evolve as the Continuous DEMs tools are updated, and not every source has useful coverage in every study area.

You do not need to understand every bundle option before starting the Newport build. We will look inside the workflow in Module 2b.
:::

---

# 3. Find Newport topobathymetric lidar

NOAA's **Digital Coast Data Access Viewer (DAV)** lets us search geographically for coastal lidar.

[Open NOAA Digital Coast DAV for Newport](https://coast.noaa.gov/dataviewer/#/lidar/search/-13815132.111459369,5557767.363023302,-13802740.194591334,5557767.363023302,-13802740.194591334,5565765.68390708,-13815132.111459369,5565765.68390708,-13815132.111459369,5557767.363023302)

:::{important}
## Your turn

Look at the lidar datasets intersecting the workshop area.

Find a dataset that:

1. overlaps the Newport DEM region
2. contains **topobathymetric lidar**
3. would be useful for a combined land-and-water DEM

Select the dataset to open its details.
:::

## Find the dataset ID

Globato/Fetchez needs the numeric DAV dataset ID.

In the dataset details:

1. Scroll to **Bulk Download**.
2. Click **Link to All Dataset Files**.
3. Look at the URL of the page that opens.

Near the end of the URL, you will see:

```text
.../####/index.html
     ↑
 DAV dataset ID
```

Use that number in:

```text
dav:survey_id=####,weight=100
```

:::{dropdown} Reveal the Newport dataset

The dataset is:

**2020 USACE NCMP Topobathy Lidar: Oregon**

DAV dataset ID:

```text
9693
```

Source specification:

```text
dav:survey_id=9693,weight=100
```
:::

:::{tip}
You do **not** need to download the lidar from DAV.

We are using DAV to discover the dataset. Once we know its ID, Globato/Fetchez can access it through the `dav` source.
:::

---

# 4. Build the Newport DEM

We now have the two main data ingredients:

```text
coupling-bathy-topo
        +
dav:survey_id=9693,weight=100
```

> **Start with strong nationally available data, then add local scientific data when it improves the DEM.**

Run:

```bash
globato build \
  -R -124.10/-124.00/44.59/44.64 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E 0.1111111s \
  -O newport \
  -D ~/workshop/newport_dem \
  --shared-cache ~/workshop/newport_data \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

:::{important}
## Start this command now

Leave the Newport build running.

The terminal should begin printing workflow- and data-specific messages. **Do not wait for the DEM to finish.**

Once the build is running, continue to **Module 2b — Inside the Workflow**.
:::

:::{dropdown} What does the build command specify?

| Part | Purpose |
|---|---|
| `-R -124.10/-124.00/44.59/44.64` | Newport study-area bounds |
| `-X 6:5` | 6 cells of overlap between adjacent DEM tiles; 5% source-data processing buffer |
| `-P epsg:4269+5703` | NAD83 + NAVD88 target reference system |
| `-E 0.1111111s` | Output resolution |
| `-O newport` | Output name |
| `-D ~/workshop/newport_dem` | Processing/output directory |
| `--shared-cache ~/workshop/newport_data` | Prepared Newport source-data cache |
| `coupling-bathy-topo` | Reusable national coastal recipe |
| `dav:survey_id=9693,weight=100` | Local Newport topobathymetric lidar |

The command combines **where and how to build the DEM** with **which data recipe to use**.
:::

:::{dropdown} If the workshop directories are missing

Module 1 should already have prepared the Newport cache and output directory.

If needed, rerun:

```bash
bash ~/shared/setup_workshop.sh
```

Then confirm:

```bash
ls ~/workshop
```
:::

---

# Continue while Newport builds

Keep the Newport terminal running and move to:

> **Module 2b — Inside the Workflow**

There we will connect the messages appearing in the terminal to the major processing stages.

:::{important}
## Ready for Module 2b?

You are ready when:

- [ ] you found or were given DAV dataset ID `9693`
- [ ] the Newport `globato build` command is running
- [ ] the terminal is printing workflow messages

You do **not** need to wait for the DEM to finish.
:::
