---
title: "2b - Inside the Workflow"
---

# Inside the Workflow

The Newport DEM should now be running.

Keep the terminal visible. You do **not** need to interpret every message—just recognize the major stages:

```text
discover data
      ↓
prepare each source
      ↓
standardize reference systems
      ↓
prioritize and stack measurements
      ↓
interpolate across multiple scales
      ↓
write the final DEM
```

:::{tip}
As the build runs, ask:

> **Which stage of the workflow are we seeing right now?**
:::

---

# The tools working together

| Tool | Main role |
|---|---|
| **Fetchez** | Orchestrates the recipe and data pipeline |
| **Transformez** | Supports horizontal and vertical reference transformations |
| **Globato** | Provides elevation-specific filtering, gridding, and DEM construction |

We launch the build with **Globato**, while Fetchez coordinates the data pipeline and Transformez supports reference-system transformations.

Our Newport recipe combines:

```text
coupling-bathy-topo
        +
dav:survey_id=9693,weight=100
```

:::{figure} ../../assets/images/workflow_final.png
:alt: End-to-end coastal DEM workflow showing Fetchez orchestration, national and local source data, Transformez and Globato processing, stacked measurements, interpolation, the final DEM and spatial metadata, and independent validation with ICESat-2.
:width: 85%
:align: center

**Coastal DEM workflow.** The build moves from data discovery through source preparation, reference-system standardization, DEM construction, and final outputs.
:::

---

# 1. Discover the data

The workflow first determines which configured sources overlap Newport.

Fetchez provides a common way to work with data from different catalogs, APIs, formats, and providers.

:::{important}
## Look at the terminal

Can you recognize the name of **one dataset or provider** being discovered, opened, or reused from cache?
:::

:::{dropdown} Explore Fetchez

Optional commands for a second terminal:

```bash
fetchez modules list
fetchez modules info dav
```

These let you inspect the available data modules and learn more about a specific module, such as the Digital Coast DAV source.
:::

:::{dropdown} About the workshop cache

The command uses:

```text
--shared-cache ~/workshop/newport_data
```

The workshop setup staged prepared source data there so the build can reuse them rather than download everything again.

```text
~/workshop/newport_data/    → prepared source-data cache
~/workshop/newport_dem/     → processing files and outputs
```

Messages about reusing cached data are expected.
:::

---

# 2. Prepare each source

Coastal elevation datasets are not all equivalent.

A reproducible workflow does not have to treat every source the same—it can apply source-specific processing within one consistent framework.

:::{dropdown} Why does the coastline matter?

A coastal DEM combines measurements collected over both land and water, often from sources with different sensors and data characteristics.

Land/water constraints can help prevent topographic measurements or interpolation from extending inappropriately into bathymetric areas, and vice versa. The coastline is therefore part of the processing logic, not just a map feature.
:::

---

# 3. Standardize the reference system

Our target Newport DEM is:

```text
epsg:4269+5703
```

or:

```text
Horizontal: NAD83
Vertical:   NAVD88
```

Source datasets may arrive in different horizontal or vertical reference systems. Before they can be combined, they need to be expressed in the requested common system.

This is where **Transformez** supports the workflow.

:::{important}
## Look at the terminal

Can you spot a message related to reprojection, transformation, EPSG codes, or datums?

You only need to recognize the stage.
:::

:::{dropdown} Why is the vertical datum important?

The same physical location can have different numerical elevations when referenced to different vertical surfaces.

A vertical transformation changes the **reference used to describe the elevation**, not the physical terrain or seafloor.
:::

:::{dropdown} Explore Transformez

In a second terminal:

```bash
transformez list
```

You do not need to run transformations manually. Fetchez coordinates the workflow and uses Transformez where needed.
:::

---

# 4. Prioritize and stack measurements

Once prepared and standardized, measurements are brought into a common raster framework.

Where datasets overlap, recipe priorities help determine which information should dominate.

For Newport, we added:

```text
dav:survey_id=9693,weight=100
```

so the selected local topobathymetric lidar receives strong priority where it overlaps lower-priority sources.

:::{note}
A source weight is a **recipe priority**, not a formal measurement uncertainty.
:::

:::{dropdown} If you see Z, W, and U

A useful shorthand is:

| Value | Meaning |
|---|---|
| `Z` | Elevation |
| `W` | Source priority / weight |
| `U` | Uncertainty information when available |

A default `U=0` means no explicit uncertainty value was provided. It does **not** mean zero real-world uncertainty.
:::

---

# 5. Interpolate across multiple scales

Measurement density can vary dramatically across a coastal region.

Dense lidar can support fine spatial detail, while sparse bathymetric observations require information to be integrated across broader scales.

> **Use fine spatial information where observations support it, and progressively broader information where measurements are sparse.**

:::{important}
## Look at the terminal

Can you spot processing at different scales or resolutions?

The goal is simply to recognize when the workflow has moved from **preparing measurements** to **building the continuous surface**.
:::

---

# 6. Write the final outputs

When processing is complete, Globato writes the final DEM and supporting products.

The two primary Newport products are:

```text
~/workshop/newport_dem/newport_n44x64_w124x10_final.tif
~/workshop/newport_dem/newport_n44x64_w124x10_hs.tif
```

```text
*_final.tif   → finished coastal DEM
*_hs.tif      → hillshade for visual inspection
```

In **Module 3**, we will also use:

```text
~/workshop/newport_dem/newport_n44x64_w124x10_sm.gpkg
```

This spatial metadata GeoPackage lets us see **which source data support different parts of the DEM**.

:::{dropdown} Other supporting outputs

Globato can also create:

| Output | Purpose |
|---|---|
| **Source masks** | Show where individual input files contribute |
| **Sources VRT** | Combines source-data coverage |
| **Spatial metadata** (`*_sm.gpkg`) | GIS view of source support |
| **Spatial metadata style** (`*_sm.qml`) | Prepared QGIS symbology |
| **Provenance raster** | Compact cell-by-cell source provenance |
| **Stack raster** | Accumulated information used during gridding |

You do not need to inspect all of these during the workshop.
:::

:::{dropdown} If your build finishes early

Confirm the primary products:

```bash
ls -lh \
  ~/workshop/newport_dem/newport_n44x64_w124x10_final.tif \
  ~/workshop/newport_dem/newport_n44x64_w124x10_hs.tif
```

We will interpret them in Module 3.
:::

---

# One last look at the terminal

Which stage best describes what you see?

- discovering or accessing data
- preparing or filtering a source
- transforming coordinates or elevations
- stacking measurements
- interpolating across multiple scales
- writing final outputs

:::{tip}
Participants may be at different stages because cached files and processing times differ. Matching the instructor's exact terminal line is not important.
:::

---

:::{dropdown} If your Newport build did not finish

Known-good Newport fallback files are available in:

```text
~/workshop/reference_outputs/newport/
```

If an instructor directs you to use them, stop the running build with **Ctrl+C** if necessary, then run:

```bash
rm -rf ~/workshop/newport_dem
mkdir -p ~/workshop/newport_dem
cp -a ~/workshop/reference_outputs/newport/. ~/workshop/newport_dem/
cd ~/workshop/newport_dem
ls -lh
```

The fallback set contains:

```text
newport_n44x64_w124x10_final.tif
newport_n44x64_w124x10_hs.tif
newport_n44x64_w124x10_sm.gpkg
newport_n44x64_w124x10_sm.qml
```

That is enough to continue with Module 3.
:::

:::{important}
## Ready for Module 3?

Continue when either:

- your Newport build has finished, **or**
- the known-good Newport files are in `~/workshop/newport_dem/`.

Next:

> **Module 3 — Explore the DEM**
:::
