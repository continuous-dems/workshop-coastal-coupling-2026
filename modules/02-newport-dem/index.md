---
title: "2 - Build the Newport Coastal DEM"
---

# Build the Newport Coastal DEM

Now that we are set up in JupyterHub, we will build a high-resolution coastal digital elevation model (DEM) for **Newport, Oregon**.

Our goal is to answer a practical question:

> **If we want to build a coastal DEM for an area, how can we efficiently discover, process, standardize, combine, and evaluate the available elevation data?**

Rather than manually downloading and preparing every dataset, we will use a standardized Continuous DEMs recipe as our starting point.

## Our study area

The Newport region includes Yaquina Bay, the entrance channel, surrounding coastal waters, and adjacent topography.

Our region of interest is:

```text
West:  -124.10
East:  -124.00
South:   44.59
North:   44.64
```

We will generate a **1/9-arc-second coastal DEM** referenced horizontally to NAD83 and vertically to NAVD88.

---

# Start with a standardized coastal recipe

Globato includes a built-in data bundle called:

```text
coupling-bathy-topo
```

A bundle provides a predefined starting recipe containing commonly useful elevation-data sources and their relative priorities.

We can inspect the bundle with:

```bash
fetchez modules bundles info coupling-bathy-topo
```

You should see a summary similar to:

```text
📜 BUNDLE SUMMARY: coupling-bathy-topo
============================================================
  Description : Data bundle for the Coastal Coupling Workshop.

  Data Sources:
    + csb
     ⤷ weight: 0.25

    + nos_hydro
     ⤷ datatype: bag
     ⤷ weight: 3.0

    + nos_hydro
     ⤷ datatype: xyz
     ⤷ weight: 0.35

    + charts
     ⤷ weight: 0.15

    + tnm
     ⤷ weight: 5.0

    + ehydro
     ⤷ weight: 1.25
     ⤷ min_year: 2025
     ⤷ max_year: 2026
============================================================
```

:::{note}
The exact bundle contents may evolve as the Continuous DEMs tools are updated. The important concept is that the bundle defines a **standard starting recipe** for coastal DEM generation.
:::

## Why use a bundle?

For a typical U.S. coastal DEM, there are several data sources that we will commonly want to search:

- topographic elevation
- NOAA hydrographic surveys
- USACE hydrographic surveys
- chart-derived bathymetry
- crowdsourced bathymetry
- other available coastal elevation data

Instead of specifying every common source individually each time we build a DEM, we can capture those choices in a reusable bundle.

The basic idea is:

```text
standard coastal recipe
        +
region of interest
        ↓
discover available data
        ↓
process and standardize them
        ↓
build coastal DEM
```

---

# Add local knowledge

A standardized bundle gives us a useful starting point, but it does not mean that every useful local dataset should be included in the bundle.

For Newport, we know that a high-quality **topobathymetric lidar survey** is available from NOAA Digital Coast.

The survey identifier is:

```text
9693
```

Rather than making this Newport-specific survey part of the general coastal bundle, we can add it when we run the workflow:

```text
dav:survey_id=9693,weight=100
```

This tells the workflow:

- `dav` — use the Digital Coast lidar data source
- `survey_id=9693` — use this specific survey
- `weight=100` — give this high-quality dataset strong priority

This illustrates an important idea:

> **Start with a standardized recipe, then improve it with local data or scientific knowledge when appropriate.**

Bundles are not rigid presets. Sources can be added, changed, or removed before the workflow runs.

For example, a predefined module can also be excluded with a modifier such as:

```bash
--modifier exclude_module:modules=MODULE_NAME
```

We will not need that modifier for today's Newport build, but it demonstrates that the standard recipe remains customizable.

---

# Build the Newport DEM

Now run the full Newport workflow:

```bash
globato build \
  -R -124.1/-124/44.59/44.64 \
  -E .11111111s \
  -P epsg:4269+5703 \
  -X 6:5 \
  -O newport \
  -D newport_cudem \
  --shared-cache coupling-shared-dir \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

:::{important}
## Start this command now

The DEM will take several minutes to build.

While it is running, we will follow the source data through the workflow and discuss what the processing messages mean.
:::

## What does the command specify?

| Option | Meaning |
|---|---|
| `globato build` | Build and execute a Globato DEM workflow |
| `-R` | Region of interest |
| `-E .11111111s` | Output resolution of approximately 1/9 arc-second |
| `-P epsg:4269+5703` | NAD83 horizontal reference and NAVD88 vertical datum |
| `-X 6:5` | Multiresolution DEM-generation settings used for this workflow |
| `-O newport` | Output/project name |
| `-D newport_cudem` | Directory for processing and output files |
| `--shared-cache coupling-shared-dir` | Use the shared workshop source-data cache |
| `coupling-bathy-topo` | Standard coastal data bundle |
| `dav:survey_id=9693,weight=100` | Newport-specific topobathymetric lidar |

---

# Why are coastal DEMs challenging?

Coastal DEMs need to combine datasets that can differ substantially in:

- source
- file format
- spatial resolution
- measurement density
- collection method
- coordinate system
- vertical datum
- age
- quality
- uncertainty

A coastal DEM workflow therefore needs to do more than simply put all available elevations into one raster.

## Dense topography and sparse bathymetry

One of the fundamental challenges is the difference in measurement density between land and water.

Topographic lidar may contain millions of closely spaced elevation measurements:

```text
• • • • • • • • • •
• • • • • • • • • •
• • • • • • • • • •
```

Nearby bathymetric measurements may be much more widely spaced:

```text
•                 •


          •


                    •
```

If a conventional interpolation method is applied without appropriate constraints, dense terrestrial elevations can influence the surface too far into sparsely measured bathymetric areas.

The workflow therefore needs to account for both **data quality** and **data density**.

---

# Why is the coastline important?

A land/water boundary is particularly important when generating a high-resolution coastal DEM.

There are several reasons.

## Constraining interpolation

We generally do not want dense topographic measurements influencing bathymetric interpolation across the coastline.

A coastline provides an important spatial constraint between the terrestrial and marine portions of the DEM.

## Lidar water-surface returns

Some topographic lidar datasets contain measurements from the **water surface**.

For a coastal DEM, we generally want the underlying terrain, riverbed, or seafloor elevation rather than the elevation of the water surface when the lidar was collected.

The workflow can use an automatically generated coastline or land/water mask to remove inappropriate topographic lidar returns over water before the data are incorporated into the DEM.

:::{note}
## Hydroflattened rivers and lakes

Artificially hydroflattened rivers and lakes are a related but different problem.

Some topographic DEM products intentionally replace natural water-surface topography with artificial flat or smoothly sloped surfaces.

Continuous DEMs also includes processing that can identify hydroflattened rivers and lakes and remove those artificial elevations when appropriate.

The distinction is:

- **Lidar water-surface returns** are measurements of the physical water surface.
- **Hydroflattened DEM surfaces** are artificial elevations introduced during DEM processing.

Both can be inappropriate when our goal is to generate a continuous topobathymetric terrain surface.
:::

---

# Follow the workflow in the terminal

As the DEM builds, many messages will scroll through the terminal.

You do not need to understand every line.

Instead, look for several major stages.

---

## 1. Data discovery

The workflow determines which configured datasets overlap the Newport region.

Different data providers have different:

- APIs
- catalogs
- file structures
- naming conventions
- download procedures

The Continuous DEMs tools provide a common interface for working with these different sources.

This source discovery and data-access functionality is primarily handled through **Fetchez**.

---

## 2. Different data formats

The workflow may encounter many different data formats, including:

```text
BAG
XYZ
CSV
ZIP
GeoTIFF
lidar point clouds
```

Each source may require different processing before the measurements can be combined.

The workflow standardizes those operations so that the user does not need to manually build a separate processing pipeline for every provider.

---

# Shared workshop data

For today's workshop, approximately 5–10 GB of downloaded source data and supporting files have been staged in shared storage.

The workflow uses:

```text
coupling-shared-dir
```

as its shared cache.

This prevents every participant from downloading a separate copy of the same source data.

The normal logic is:

```text
Is the source already cached?
        │
        ├── Yes → use it
        │
        └── No  → discover and download it
```

The scientific processing workflow is otherwise the same.

Participant-specific DEM processing and outputs remain in each participant's working directory.

---

# Horizontal and vertical reference systems

The different source datasets cannot necessarily be combined directly.

Their coordinates and elevations may be referenced to different horizontal coordinate systems and vertical datums.

For example, coastal elevation datasets may be referenced to:

- NAVD88
- Mean Lower Low Water
- other tidal datums
- ellipsoidal heights
- other geodetic reference systems

Our Newport DEM is requested in:

```text
epsg:4269+5703
```

which represents:

```text
Horizontal: NAD83
Vertical:   NAVD88
```

Watch the terminal for coordinate and vertical transformation messages.

The workflow brings the different source measurements into the requested common reference system before they are combined.

This transformation functionality is supported by **Transformez** and the broader Continuous DEMs transformation framework.

---

# Source-specific processing

Different source datasets can require different processing.

This is an important feature of the workflow:

> **We do not assume that every elevation dataset should be treated exactly the same way.**

Examples include:

- unpacking ZIP archives
- identifying the relevant files within an archive
- clipping data to the requested region
- removing elevation outliers
- converting source formats
- transforming coordinate systems
- masking inappropriate water-surface elevations
- detecting and removing hydroflattened water bodies

These operations can be applied automatically according to the source and recipe.

---

# Data priority: weights

As datasets are processed, you may see terminal output containing:

```text
Z: [...]
W: [...]
U: [...]
```

These represent important information carried through the workflow.

## Z — elevation

`Z` represents elevation.

The reported elevation range provides a useful basic quality-control check.

Ask:

- Are the values reasonable for this location?
- Are bathymetric elevations generally negative relative to NAVD88 where expected?
- Are there unexpectedly large positive or negative values?

## W — weight

`W` represents the relative priority assigned to the source.

For example, our Newport-specific topobathymetric lidar has:

```text
weight=100
```

while lower-priority supplemental bathymetric sources receive smaller weights.

The important concept is:

> **When datasets overlap, higher-quality or more appropriate data can be given greater priority.**

A weight is not the same thing as measurement uncertainty. It represents how the source should be prioritized in the DEM-building recipe.

## U — uncertainty

`U` represents uncertainty information.

The framework can carry uncertainty information through the workflow, although not every source used in today's example provides an explicit uncertainty value or uncertainty grid.

You may therefore see:

```text
U: 0
```

for some datasets.

---

# Provenance

A finished DEM should not be a black box.

The workflow keeps track of the source datasets that contribute to the DEM.

You may see output referring to:

```text
provenance
source masks
audit
download report
```

These products help answer questions such as:

- Which dataset contributed here?
- Where does the topobathymetric lidar contribute?
- Where do NOAA hydrographic surveys contribute?
- Which areas required more interpolation?
- Which source was used where several datasets overlapped?

We will inspect these products after the break.

---

# Raster stacking

After the source measurements have been discovered, processed, and transformed into a common reference system, they are placed into a common raster framework.

This is the **stacking** stage.

Where several datasets overlap, source weights help determine which measurements have priority.

Conceptually:

```text
high-priority measurements
          ↓
lower-priority measurements
          ↓
supplemental observations
          ↓
interpolation where measurements are absent
```

---

# Multiresolution processing

This is one of the central scientific concepts in the workflow.

Coastal elevation measurements have dramatically different spatial densities.

A single interpolation scale is not ideal for both:

```text
very dense lidar
```

and:

```text
widely spaced bathymetric measurements
```

Instead, the workflow builds progressively coarser representations of the source data.

Conceptually:

```text
fine resolution
      ↓
slightly coarser
      ↓
coarser
      ↓
broad regional scale
```

The key idea is:

> **Use the finest spatial scale where the observations support it, and progressively broaden the interpolation scale as the measurements become sparser.**

This allows dense datasets such as lidar to preserve fine-scale terrain information while allowing much broader interpolation where bathymetric observations are sparse.

## Why not simply interpolate everything at the finest resolution?

Consider sparse bathymetric measurements:

```text
•                         •


              •


                              •
```

There may not be enough information to realistically estimate very fine-scale terrain everywhere between those observations.

A coarser representation better reflects the spatial support provided by the measurements.

Meanwhile, dense lidar may support much finer detail:

```text
• • • • • • • •
• • • • • • • •
• • • • • • • •
```

Multiresolution processing lets these very different measurement densities coexist within the same DEM workflow.

---

# Blending the scales

The progressively coarser surfaces are combined into a single continuous DEM.

At a high level:

```text
measured high-resolution information
                ↓
small gaps filled at fine scales
                ↓
larger gaps use progressively coarser information
                ↓
scales combined into a continuous surface
```

For today's workshop, the important point is not every mathematical detail of the interpolation algorithm.

The important concept is:

> **The spatial density and quality of the measurements help determine the appropriate scale at which information should be incorporated into the DEM.**

---

# The finished Newport DEM

When the build completes, look in:

```text
newport_cudem/
```

for the primary DEM products.

The main outputs should include:

```text
newport_final.tif
newport_hs.tif
```

where:

- `newport_final.tif` is the finished coastal DEM
- `newport_hs.tif` is a shaded-relief visualization of the surface

---

# Explore your DEM

Open the DEM and hillshade using the JupyterLab file browser.

Spend a few minutes exploring the Newport region.

Look at:

- Yaquina Bay
- the entrance channel
- the coastline
- adjacent terrestrial terrain
- bathymetric morphology
- transitions between dense and sparse measurement areas
- areas that appear to rely more heavily on interpolation

Think about the following questions:

1. Where does the DEM contain the most detailed terrain information?
2. Where do the bathymetric measurements appear to become sparser?
3. Can you identify areas where interpolation is probably doing more work?
4. Does the coastline behave as expected?
5. Can you see the influence of the high-resolution topobathymetric lidar?
6. Is there anything in the DEM that you would want to investigate further?

:::{tip}
A DEM that looks reasonable is not automatically a good DEM.

Visual inspection is an important quality-control step, but we also want to understand the source provenance and compare the finished surface with independent observations.
:::

---

:::{important}
# Break — 3:05–3:15 p.m. ET

Take a break, or continue exploring your DEM and hillshade.

After the break, we will:

- inspect source provenance and supporting outputs
- use Fetchez directly
- use Transformez directly
- independently evaluate the Newport DEM using ICESat-2 observations
:::

