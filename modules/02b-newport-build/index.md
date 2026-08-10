---
title: "2b - Follow the Newport Build"
---

# Follow the Newport Build

The Newport DEM should now be running.

Keep the terminal visible as we walk through the major stages of the workflow.

You do **not** need to interpret every message. Instead, try to recognize this overall sequence:

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
## Follow the workflow, not every log line

As the build runs, periodically ask:

> **Which stage of the workflow are we seeing right now?**

That is more useful than trying to understand every terminal message.
:::

---

# The tools working together

Three Continuous DEMs tools support this workflow:

| Tool | Main role |
|---|---|
| **Fetchez** | Discover and access source elevation data |
| **Transformez** | Support horizontal and vertical transformations |
| **Globato** | Coordinate the recipe and build the DEM |

We launched the workflow with **Globato**.

Globato then uses the other parts of the framework as needed to execute the recipe.

Our Newport recipe combines:

```text
coupling-bathy-topo
        +
dav:survey_id=9693,weight=100
```

The bundle provides the strong nationally available starting point we discussed in Module 2.

The DAV survey adds the locally useful Newport topobathymetric lidar.

---

# 1. Discover and access the data

The workflow first determines which configured data sources actually overlap the Newport study area.

Different providers may use different:

- catalogs
- APIs
- file structures
- formats
- download methods

Fetchez gives the larger workflow a common way to access those sources.

As the terminal runs, watch for data being:

- discovered
- opened
- downloaded
- reused from cache

:::{important}
## Look at what Newport actually has

The recipe may list many possible data sources, but only sources with relevant coverage contribute to this DEM.

This is one reason the same recipe can be reused in different coastal regions.
:::

---

# Shared workshop data

Our command includes:

```text
--shared-cache coupling-shared-dir
```

The logic is simple:

```text
source already cached?
        │
        ├── yes → reuse it
        │
        └── no  → access it
```

For the workshop, this avoids unnecessary duplicate downloads.

Your own Newport processing and output files are still written to:

```text
newport_cudem/
```

---

# 2. Prepare each source appropriately

Coastal elevation datasets are not all equivalent.

Depending on the source, the workflow may need to:

- clip data to the study area
- unpack or convert files
- filter inappropriate elevations
- apply land/water constraints
- transform coordinates or vertical datums

The key idea is:

> **Use one reproducible workflow while still treating each source according to what that source requires.**

This is especially important when combining topography and bathymetry collected with very different sensors and for different purposes.

---

:::{dropdown} Why does the coastline matter?

A coastal DEM crosses a difficult boundary between dense terrestrial measurements and often sparser bathymetric observations.

We do not want dense land elevations influencing interpolation far offshore.

A land/water boundary can also help distinguish useful terrain or seafloor measurements from inappropriate water-surface elevations.

Conceptually:

```text
land measurements      water measurements
        \                    /
         \                  /
          coastline constraint
                  ↓
          coastal DEM surface
```

The coastline is therefore part of the processing logic, not just a map feature.

:::

---

# 3. Standardize the reference system

Our target DEM is requested in:

```text
epsg:4269+5703
```

which represents:

```text
Horizontal: NAD83
Vertical:   NAVD88
```

Source datasets may arrive in different horizontal or vertical references.

Before those measurements can be combined meaningfully, they need to be expressed in the requested common reference system.

Watch the terminal for transformation steps.

This is where **Transformez** and the broader transformation framework support the workflow.

:::{important}
## One surface, one reference system

Measurements collected in different datums cannot simply be stacked together as if their elevation values mean the same thing.

Horizontal and vertical standardization is a fundamental part of building a defensible coastal DEM.
:::

---

# 4. Prioritize and stack the measurements

Once the source data are prepared and standardized, the measurements are brought into a common raster framework.

Where sources overlap, their relative priorities help determine which information should dominate.

Conceptually:

```text
higher-priority measurements
          ↓
lower-priority measurements
          ↓
supplemental observations
          ↓
remaining gaps
```

For Newport, we explicitly added:

```text
dav:survey_id=9693,weight=100
```

so the locally selected topobathymetric lidar receives strong priority where it overlaps lower-priority sources.

:::{note}
A source weight is a **recipe priority**, not a formal measurement uncertainty.
:::

---

:::{dropdown} If you see Z, W, and U in the terminal

You may see output containing:

```text
Z: [...]
W: [...]
U: [...]
```

A useful shorthand is:

| Value | Meaning |
|---|---|
| `Z` | Elevation |
| `W` | Source priority / weight |
| `U` | Uncertainty information when available |

The reported `Z` range can provide a quick reality check on the elevations being processed.

`W` reflects the recipe priorities used when sources overlap.

Not every input provides explicit uncertainty information, so `U` may be absent or zero for some sources.

:::

---

# 5. Interpolate across multiple scales

Measurement density can vary dramatically across a coastal region.

Dense lidar may support very fine spatial detail:

```text
• • • • • • • •
• • • • • • • •
• • • • • • • •
```

Bathymetric observations may be much farther apart:

```text
•                    •


          •


                         •
```

A single interpolation scale is not ideal for both situations.

The Newport build uses:

```text
-X 6:5
```

for multiresolution processing.

The key idea is:

> **Use fine spatial information where the observations support it, and progressively broader information where measurements are sparse.**

Conceptually:

```text
dense measurements
        ↓
fine-scale information

sparser measurements
        ↓
broader interpolation
```

The detailed interpolation mathematics are not the focus of this workshop.

What matters is why multiple spatial scales are useful when measurement density changes across the DEM.

---

# What stage are we in now?

Look back at the terminal.

Can you recognize any of these?

- discovering or accessing data
- reusing cached data
- filtering or preparing a source
- transforming coordinates or elevations
- stacking measurements
- processing progressively coarser scales
- writing final outputs

The goal is to understand the **shape of the workflow**.

---

# When the build finishes

Confirm that the two primary products were created:

```bash
ls -lh newport_cudem/newport_final.tif newport_cudem/newport_hs.tif
```

You should see:

```text
newport_final.tif   finished Newport coastal DEM
newport_hs.tif      hillshade for visual inspection
```

:::{important}
## Build complete

Do not spend much time interpreting the finished DEM yet.

In **Module 3 — Explore and Evaluate the DEM**, we will inspect the surface and provenance and evaluate the DEM with independent observations.
:::

---

# Short break

Once the Newport build has finished, you you can view the outputs. This is a good point to take a short workshop break if needed.
