---
title: "4 - Same Recipe, New Region"
---

# Same Recipe, New Region

We built and explored a coastal DEM for **Newport, Oregon**.

Now we will reuse the same workflow in a very different setting:

> **Sarasota, Florida**

```text
keep the reusable recipe
          +
change the local pieces
          ↓
new coastal DEM
```

We will launch Sarasota and then **leave it running** while we move to Module 5.

---

# 1. Move to Sarasota

Our Sarasota study area is:

```text
West:  -82.59
East:  -82.53
South:  27.28
North:  27.34
```

:::{figure} ../../assets/images/sarasota_aoi.png
:alt: Satellite and aerial imagery of the Sarasota, Florida workshop study area around Siesta Key, with the final DEM region outlined in red.
:width: 100%
:align: center

**Sarasota, Florida Study Area.** The red box shows the DEM area.
:::

Compared with Newport, Sarasota is a **low-relief Gulf coast with shallow nearshore waters**.

The setting changes substantially.

The workflow does not need to start over.

---

# 2. Change only what is local

Keep:

```text
coupling-bathy-topo
-P epsg:4269+5703
-X 6:5
-E 0.1111111s
```

Change:

```text
study-area bounds
local DAV dataset
output name
output directory
shared-cache location
```

:::{important}
## The transfer question

> **What local dataset should replace the Newport lidar?**
:::

:::{dropdown} Newport → Sarasota comparison

| Setting | Newport | Sarasota |
|---|---|---|
| Region | `-124.10/-124.00/44.59/44.64` | `-82.59/-82.53/27.28/27.34` |
| National bundle | `coupling-bathy-topo` | `coupling-bathy-topo` |
| Local DAV dataset | `9693` | `10196` |
| Output name | `newport` | `sarasota` |
| Output directory | `~/workshop/newport_dem` | `~/workshop/sarasota_dem` |
| Prepared cache | `~/workshop/newport_data` | `~/workshop/sarasota_data` |
| Resolution | ~1/9 arc-second | ~1/9 arc-second |
| Combined reference system | `epsg:4269+5703` | `epsg:4269+5703` |
| Tile overlap / source buffer | `-X 6:5` | `-X 6:5` |

The reusable scientific recipe stays the same. The study area, local data, and output locations change.
:::

---

# 3. Find the Sarasota lidar

[Open NOAA Digital Coast DAV for Sarasota](https://coast.noaa.gov/dataviewer/#/lidar/search/-9198602.505474905,3155893.0057734232,-9186314.531330857,3155893.0057734232,-9186314.531330857,3166415.89275869,-9198602.505474905,3166415.89275869,-9198602.505474905,3155893.0057734232)

:::{important}
## Your turn

Find a dataset that:

1. overlaps the Sarasota DEM region
2. contains **topobathymetric lidar**
3. is the **most recent** dataset that meets those criteria

Use the same DAV dataset-ID method you used for Newport.
:::

:::{dropdown} Need a reminder for finding the DAV dataset ID?

In the dataset details:

1. Scroll to **Bulk Download**.
2. Click **Link to All Dataset Files**.
3. Find the number near the end of the URL:

```text
.../XXXXX/index.html
      ↑
  dataset ID
```

Then use:

```text
dav:survey_id=XXXXX,weight=100
```
:::

:::{dropdown} Reveal the Sarasota dataset

**2024 USACE FEMA Topobathy Lidar: Post Hurricane Milton, FL**

DAV dataset ID:

```text
10196
```

Local source:

```text
dav:survey_id=10196,weight=100
```

Newport used `9693`; Sarasota uses `10196`. The source syntax stays the same.
:::

:::{tip}
You do **not** need to download the lidar from DAV.

The workshop setup already staged the Sarasota source-data cache at:

```text
~/workshop/sarasota_data
```
:::

---

# 4. Launch the Sarasota build

Run:

```bash
globato build \
  -R -82.59/-82.53/27.28/27.34 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E 0.1111111s \
  -O sarasota \
  -D ~/workshop/sarasota_dem \
  --shared-cache ~/workshop/sarasota_data \
  coupling-bathy-topo \
  dav:survey_id=10196,weight=100
```

:::{important}
## Success check

You are ready to move on when:

- [ ] You included the DAV dataset `10196`
- [ ] The Sarasota `globato build` command is running

**Leave this terminal running.**

Open a **new terminal** for Module 5 so the build can continue uninterrupted.
:::

:::{dropdown} If the Sarasota directories are missing

Rerun:

```bash
bash ~/shared/setup_workshop.sh
```

Then confirm:

```bash
ls -ld ~/workshop/sarasota_data ~/workshop/sarasota_dem
```
:::

::::{dropdown} Sneak peek: Sarasota DEM

:::{figure} ../../assets/images/sarasota_hs.png
:alt: Hillshade of the Sarasota, Florida coastal DEM.
:width: 100%
:align: center

**Sarasota coastal DEM.** Module 5 will return to this DEM after evaluating Newport with IVERT.
:::

::::

---

# The takeaway

```text
same recipe + new local choices = new local DEM
```

You changed the **study area, local lidar, and output locations** while keeping the reusable coastal recipe and core processing settings.

The Sarasota build shows how the same workflow can be transferred to a new coastal region by changing only the local choices.

Next, we ask the question that matters after any DEM is built:

> **How well does it agree with independent observations?**

**Next: Module 5 — Evaluate the DEMs with IVERT**


