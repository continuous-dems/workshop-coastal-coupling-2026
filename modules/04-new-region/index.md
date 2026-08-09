---
title: "4 - Same Recipe, New Region"
---

# Same Recipe, New Region

We have now built, inspected, and evaluated a coastal DEM for **Newport, Oregon**.

For our final exercise, we will ask:

> **How much of the workflow needs to change if we want to build a coastal DEM somewhere completely different?**

One of the main goals of the Continuous DEMs project is to make coastal DEM generation **reusable and reproducible**.

Instead of rebuilding a workflow from scratch for every location, we can reuse the same standardized recipe and change only the parts that are specific to the new study area.

---

# Our second study area

For our second example, we will move from the Pacific Northwest to the **Sarasota, Florida** coast.

Our new region of interest is:

```text
West:  -82.59
East:  -82.53
South:  27.28
North:  27.34
```

We will use the same output resolution and reference system as the Newport exercise:

```text
Resolution: 1/9 arc-second
Horizontal: NAD83
Vertical:   NAVD88
```

represented by:

```text
epsg:4269+5703
```

---

# What stays the same?

Most of the workflow does not need to change.

We will continue to use:

```text
coupling-bathy-topo
```

as our standardized coastal data recipe.

We will also keep the same:

- 1/9-arc-second output resolution
- NAD83 horizontal reference
- NAVD88 vertical datum
- multiresolution DEM settings
- overall data-discovery, processing, stacking, and interpolation workflow

Conceptually:

```text
standard coastal recipe
        +
local knowledge
        +
region of interest
        ↓
data discovery
        ↓
source-specific processing
        ↓
horizontal and vertical standardization
        ↓
data weighting and stacking
        ↓
multiresolution interpolation
        ↓
final coastal DEM
```

The key idea is:

> **The study area changes, but the reusable workflow does not need to be rebuilt from scratch.**

---

# What changes?

For Sarasota, we need a new region of interest and new output names.

We also use a **different locally appropriate topobathymetric lidar survey**.

For Newport, we added:

```text
dav:survey_id=9693,weight=100
```

For Sarasota, we will use NOAA Digital Coast DAV to identify an appropriate local topobathymetric lidar survey before we run the build.

This is important.

The `coupling-bathy-topo` bundle represents the **reusable part of the recipe**.

The Digital Coast survey is **local knowledge** that can change from one study area to another.

---

# Find the local topobathymetric lidar

Before running the Sarasota build, let's see how we can find a useful local lidar survey ourselves.

NOAA's **Digital Coast Data Access Viewer (DAV)** lets us explore available coastal lidar geographically.

Open DAV already focused on our Sarasota workshop area:

[Open NOAA Digital Coast DAV for the Sarasota study area](https://coast.noaa.gov/dataviewer/#/lidar/search/-9198602.505474905,3155893.0057734232,-9186314.531330857,3155893.0057734232,-9186314.531330857,3166415.89275869,-9198602.505474905,3166415.89275869,-9198602.505474905,3155893.0057734232)

::{important}
## Focus on our Sarasota study area

The link opens DAV around the same small region we are using for this exercise:

```text
West:  -82.59
East:  -82.53
South:  27.28
North:  27.34
```

We are interested in lidar coverage that is useful for this specific coastal DEM.
:::

Look through the lidar datasets available in this area.

As you inspect the results, ask:

- Which dataset covers our study area?
- Which dataset includes **topobathymetric lidar**?
- Which survey looks most appropriate to add to our coastal DEM recipe?

For this Sarasota example, the useful Digital Coast survey is:

```text
survey_id=10196
```

That survey ID can be passed directly to `globato` as:

```text
dav:survey_id=10196,weight=100
```

This is a useful example of how **local knowledge enters a reusable recipe**:

```text
define the region
        ↓
explore available data in NOAA Digital Coast DAV
        ↓
identify an appropriate local survey
        ↓
add its DAV survey ID to the standard recipe
```

The `coupling-bathy-topo` bundle remains our reusable starting point. The locally appropriate survey can change from one study area to another.

---

# Build the Sarasota DEM

Run:

```bash
globato build \
  -R -82.59/-82.53/27.28/27.34 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E 0.1111111s \
  -O sarasota \
  -D sarasota_dem \
  --shared-cache shared/sarasota_data \
  coupling-bathy-topo \
  dav:survey_id=10196,weight=100
```

:::{important}
## Start the Sarasota build

This command uses the same standardized coastal recipe and DEM settings as the Newport exercise, but applies them to a new region with a different locally appropriate topobathymetric lidar survey.
:::

---

# Compare the two examples

The major differences are:

| Option | Newport | Sarasota |
|---|---|---|
| Region | `-124.1/-124/44.59/44.64` | `-82.59/-82.53/27.28/27.34` |
| Output name | `newport` | `sarasota` |
| Output directory | `newport_cudem` | `sarasota_dem` |
| Shared cache | `coupling-shared-dir` | `shared/sarasota_data` |
| Local Digital Coast survey | `9693` | `10196` |

The important settings remain the same:

```text
-X 6:5
-P epsg:4269+5703
-E ~1/9 arc-second
coupling-bathy-topo
```

This demonstrates the separation between:

```text
reusable recipe
        +
region-specific choices
```

---

# A bundle is a starting point

The `coupling-bathy-topo` bundle gives us a standardized starting recipe.

It does not lock us into a fixed set of inputs.

For Newport, local knowledge told us to add:

```text
dav:survey_id=9693,weight=100
```

For Sarasota, local knowledge tells us to add:

```text
dav:survey_id=10196,weight=100
```

This gives us a useful balance:

> **Make the common workflow simple, while preserving the ability to apply local scientific judgment.**

If we moved to another region, we could:

- use the standard bundle by itself
- add a different locally appropriate dataset
- change source options or weights
- remove a predefined source when needed

The goal is to reuse the workflow while still making scientifically appropriate choices for each study area.

---

# Watch the data discovery

As the Sarasota workflow runs, watch the terminal output.

You do not need to follow every message.

Instead, notice:

1. Which datasets are discovered for Sarasota?
2. How does the available source coverage differ from Newport?
3. Where does the Sarasota topobathymetric lidar contribute?

This demonstrates an important distinction:

> **The recipe can stay consistent even when the underlying data coverage changes.**

---

# Inspect the Sarasota DEM

When the workflow finishes, look inside:

```text
sarasota_dem/
```

for the resulting DEM and supporting products.

Compare the Sarasota result with Newport.

Look for differences in:

- coastline and nearshore morphology
- topographic and bathymetric detail
- measured-data coverage
- areas requiring more interpolation
- which source datasets contribute to the final DEM

Ask yourself:

> **Does the same recipe behave reasonably in two very different coastal regions?**

and:

> **Which differences come from the available data, rather than from the workflow itself?**

---

# The complete workflow

Across the workshop, we have now followed the full coastal DEM process:

```text
define a region
        ↓
start with a reusable data recipe
        ↓
add local knowledge when appropriate
        ↓
discover available source data
        ↓
apply source-specific processing
        ↓
standardize horizontal and vertical references
        ↓
prioritize and stack measurements
        ↓
interpolate across multiple spatial scales
        ↓
generate the coastal DEM
        ↓
inspect provenance
        ↓
validate with independent observations
        ↓
reuse the workflow in another region
```

---

# Workshop takeaways

## 1. Start with the scientific problem

The goal is not simply to run a software package.

The goal is to build a defensible coastal elevation model from the best available observations.

## 2. Standardize the common workflow

Bundles such as:

```text
coupling-bathy-topo
```

capture commonly useful data sources and processing choices in a reusable recipe.

## 3. Add local knowledge when it improves the result

The Newport and Sarasota examples use the same standard bundle, but each uses a different locally appropriate topobathymetric lidar survey.

## 4. Treat different source datasets appropriately

Different datasets may require different:

- transformations
- masks
- filters
- weights
- processing steps

A reproducible workflow should preserve those source-specific decisions.

## 5. Match interpolation scale to data support

Dense measurements can support fine-scale terrain information.

Sparse observations require progressively broader interpolation.

Multiresolution processing helps bring those different measurement densities together in a continuous surface.

## 6. Keep provenance

A DEM should not be a black box.

We should be able to determine where the input data came from and how they contributed to the final surface.

## 7. Validate independently

Visual inspection is necessary but not sufficient.

Independent observations such as ICESat-2 provide another way to evaluate the finished DEM and identify areas that may need further investigation.

## 8. Reuse the workflow

The most important outcome is not a single Newport or Sarasota DEM.

It is a workflow that can be applied, inspected, evaluated, and refined for other coastal regions.

---

:::{tip}
## Where to go next

Try a coastal region that matters to your own work.

Start with:

```text
coupling-bathy-topo
```

inspect which data are available, and then decide whether additional local datasets or recipe changes would improve the result.

The same workflow can become the starting point for a new coastal DEM rather than a one-off exercise.
:::

---

# Questions and discussion

What would you change for your own study area?

Consider:

- additional local datasets
- different output resolution
- another vertical datum
- source priorities
- masking requirements
- interpolation behavior
- validation data
- repeatable production across many regions

These are the kinds of choices the Continuous DEMs framework is designed to make easier to express, reproduce, and revisit.
