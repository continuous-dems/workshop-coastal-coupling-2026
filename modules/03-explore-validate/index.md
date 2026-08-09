---
title: "3 - Explore and Evaluate the DEM"
---

# Explore and Evaluate the DEM

We now have a finished coastal DEM for Newport, Oregon.

The next questions are:

> **Where did the DEM elevations come from?**

> **How can we use the underlying Continuous DEMs tools directly?**

> **How well does the finished DEM agree with independent observations?**

In this module, we will inspect the supporting Globato outputs, use Fetchez and Transformez directly, and evaluate the Newport DEM with ICESat-2 observations using IVERT.

---

# Inspect the DEM provenance

A finished DEM should not be treated as a black box.

Globato produces supporting information that helps us understand:

- which datasets were discovered
- which datasets were actually used
- where different sources contributed
- how overlapping sources were prioritized
- which areas relied more heavily on interpolation
- what processing steps were applied

Look inside the Newport project directory:

```text
newport_cudem/
```

In addition to the final DEM and hillshade, you may see supporting products related to:

```text
provenance
source masks
audit information
download reports
processing outputs
```

The exact filenames and directory structure may vary as the software evolves, but the goal is the same:

> **We should be able to trace the finished DEM back to the source datasets and processing decisions that produced it.**

## Questions to explore

As we inspect these outputs, consider:

1. Where does the Newport topobathymetric lidar contribute?
2. Where do NOAA hydrographic data contribute?
3. Where does terrestrial elevation data contribute?
4. Where do lower-priority supplemental datasets contribute?
5. Where does the DEM appear to rely more heavily on interpolation?
6. Are there places where several datasets overlap?

This provenance information is especially useful when something in the DEM looks unusual.

Instead of asking only:

> "Does this area look wrong?"

we can also ask:

> "Which source data and processing steps contributed here?"

---

# The tools behind the workflow

So far, Globato has coordinated the complete workflow for us.

At a high level:

```text
Fetchez
   ↓
data discovery and access

Transformez
   ↓
reference-system transformations

Globato
   ↓
DEM workflow orchestration and generation

IVERT
   ↓
independent DEM evaluation
```

These tools can also be used independently.

We will briefly try Fetchez and Transformez directly before evaluating the finished DEM.

---

# Use Fetchez directly

Fetchez provides a common interface for discovering, inspecting, downloading, and preparing elevation data from many different providers.

## List available data modules

Run:

```bash
fetchez modules list
```

This shows the data-source modules currently available through Fetchez.

Different modules can represent very different providers and data types, but Fetchez gives us a standardized interface for working with them.

## Inspect the coastal bundle

We can also inspect the same bundle used to build the Newport DEM:

```bash
fetchez modules bundles info coupling-bathy-topo
```

This shows the predefined source recipe that Globato used as the starting point for our coastal DEM.

Recall that we then added the Newport-specific topobathymetric lidar separately:

```text
dav:survey_id=9693,weight=100
```

The combination was:

```text
standard coastal bundle
        +
Newport-specific topobathymetric lidar
        ↓
Newport DEM recipe
```

## Inspect an individual source

For example, inspect the USACE eHydro source:

```bash
fetchez modules info ehydro
```

This lets us examine the source independently of the larger Globato workflow.

:::{tip}
The point of this exercise is not to learn every Fetchez command.

The important idea is that the same data-discovery and access logic used automatically by Globato can also be used directly when you need individual datasets.
:::

---

# Use Transformez directly

Coastal elevation datasets are frequently referenced to different vertical datums.

Before combining them, their elevations need to be expressed in a common reference system.

Transformez provides the vertical-transformation functionality used by the Continuous DEMs framework.

Start by inspecting the transformations available in the installed version:

```bash
transformez list
```

During the Newport workflow, source datasets were transformed as needed into the requested output reference:

```text
Horizontal: NAD83
Vertical:   NAVD88
```

specified as:

```text
epsg:4269+5703
```

Conceptually:

```text
source elevation
        ↓
identify source reference
        ↓
calculate vertical transformation
        ↓
apply elevation offset
        ↓
output elevation in requested datum
```

This is particularly important in coastal environments, where datasets may be referenced to:

- NAVD88
- tidal datums
- ellipsoidal heights
- other geodetic vertical references

:::{note}
A vertical datum transformation changes the reference surface used to describe elevation.

It does **not** change the physical terrain being represented.
:::

---

# Why independently evaluate the DEM?

Visual inspection is important, but a DEM can look smooth and realistic while still containing significant vertical errors.

We therefore want to compare the finished surface with elevation observations that were **not used to build the DEM**.

For this workshop, we will use ICESat-2 observations through **IVERT**.

The basic idea is:

```text
finished Newport DEM
          +
independent ICESat-2 elevations
          ↓
elevation differences
          ↓
statistics + spatial error patterns
```

Because the validation observations are independent of the source data used to generate the DEM, they provide an external check on the finished surface.

---

# Download the ICESat-2 validation data

First, make sure the ICESat-2 validation database contains observations for our Newport region.

Run:

```bash
ivert database download \
  -ds 2022.01.01 \
  -de 2024.11.06 \
  -- -124.1/-124/44.59/44.64
```

This requests ICESat-2 observations covering the Newport region for the specified date range.

For the workshop environment, these data may already be available locally.

---

# Validate the Newport DEM

Now evaluate the finished DEM:

```bash
ivert validate \
  newport_cudem/newport_final.tif \
  -V navd88 \
  -n newport \
  -mc
```

The important pieces are:

| Option | Meaning |
|---|---|
| `newport_cudem/newport_final.tif` | DEM to evaluate |
| `-V navd88` | Vertical datum of the DEM |
| `-n newport` | Name for the validation run |
| `-mc` | Generate the configured validation products |

IVERT compares the DEM elevations with the available ICESat-2 observations and generates statistical and spatial quality-control products.

---

# What should we look at?

The validation outputs can tell us both:

> **How large are the elevation differences?**

and:

> **Where do those differences occur?**

Useful summary statistics may include:

- number of validation observations
- mean error or bias
- root mean square error (RMSE)
- spread of elevation differences
- other distribution statistics

But a single summary statistic does not tell the whole story.

## Spatial error patterns

A DEM could have an acceptable overall RMSE while still containing localized areas with large errors.

That is why spatial validation products are particularly useful.

Look for:

- clusters of larger residuals
- differences between land and water
- errors near the coastline
- areas with sparse source measurements
- areas dominated by interpolation
- unusual terrain features
- regions associated with a particular source dataset

---

# Connect validation back to provenance

This is where the supporting Globato outputs become especially valuable.

If IVERT identifies an area with relatively large elevation differences, we can return to the provenance information and ask:

```text
Where is the error?
        ↓
Which source contributed there?
        ↓
How dense were the observations?
        ↓
Was the area mostly measured or interpolated?
        ↓
Was special preprocessing applied?
```

This turns validation into more than a single accuracy number.

It becomes a way to understand how the input data and processing choices affected the final surface.

---

# Think critically about the result

As you inspect the validation products, consider:

1. Where are the largest elevation differences?
2. Are the errors spatially clustered?
3. Do larger errors coincide with sparse measurement coverage?
4. Are there differences between terrestrial and bathymetric areas?
5. Do any features visible in the hillshade correspond to validation errors?
6. Does the source provenance help explain those locations?
7. Would you change anything in the input recipe based on what you see?

:::{important}
## Validation is part of the workflow

The goal is not simply:

```text
build DEM → done
```

A more useful workflow is:

```text
build DEM
    ↓
inspect
    ↓
validate
    ↓
understand errors
    ↓
refine data or processing when needed
```

A reproducible DEM workflow makes that cycle much easier to repeat.
:::

---

# What have we learned?

At this point, we have:

- built a high-resolution coastal DEM from multiple datasets
- used a standardized coastal data bundle
- added a high-quality local dataset
- followed source-specific processing and multiresolution interpolation
- inspected provenance and supporting outputs
- used Fetchez directly
- examined the role of Transformez
- independently evaluated the finished surface with ICESat-2

The final question is:

> **Can we apply the same workflow somewhere else without rebuilding the entire process from scratch?**

That is what we will do in the next module.

