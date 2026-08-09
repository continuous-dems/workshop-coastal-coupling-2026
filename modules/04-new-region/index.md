---
title: "4 - Same Recipe, New Region"
---

# Same Recipe, New Region

We have now built, inspected, and evaluated a coastal DEM for Newport, Oregon.

The final question is:

> **How much of that workflow needs to change if we want to build a coastal DEM somewhere else?**

One of the main goals of the Continuous DEMs project is to make coastal DEM generation **reusable and reproducible**.

Instead of rebuilding a workflow from scratch for every location, we can reuse the same standardized recipe and change only the parts that are specific to the new study area.

---

# Our second study area

For our second example, we will move south along the Oregon coast to the **Coos Bay region**.

Our new region of interest is:

```text
West:  -124.35
East:  -124.25
South:   43.30
North:   43.40
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

We will also continue to use the high-quality topobathymetric lidar survey:

```text
dav:survey_id=9693,weight=100
```

because this survey also covers the Coos Bay study area.

The processing logic remains the same:

```text
standard coastal recipe
        +
high-quality local lidar
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

---

# What changes?

Only a few command-line options need to change.

For Newport, we used:

```text
-R -124.1/-124/44.59/44.64
-O newport
-D newport_cudem
```

For Coos Bay, we will use:

```text
-R -124.35/-124.25/43.30/43.4
-O coos
-D coos_dem
```

The recipe itself remains the same.

This is the key idea:

> **The study area changes, but the reusable workflow does not need to be rebuilt from scratch.**

---

# Build the Coos Bay DEM

Run:

```bash
globato build \
  -R -124.35/-124.25/43.30/43.4 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E .1111111s \
  -O coos \
  -D coos_dem \
  --shared-cache coupling-shared-dir \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

:::{important}
## Start the Coos Bay build

This command uses the same standard coastal recipe and the same topobathymetric lidar source as the Newport exercise.

Only the study area and output names have changed.
:::

---

# Compare the two commands

The Newport build was:

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

The Coos Bay build is:

```bash
globato build \
  -R -124.35/-124.25/43.30/43.4 \
  -E .11111111s \
  -P epsg:4269+5703 \
  -X 6:5 \
  -O coos \
  -D coos_dem \
  --shared-cache coupling-shared-dir \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

The major differences are simply:

| Option | Newport | Coos Bay |
|---|---|---|
| Region | `-124.1/-124/44.59/44.64` | `-124.35/-124.25/43.30/43.4` |
| Output name | `newport` | `coos` |
| Output directory | `newport_cudem` | `coos_dem` |

Everything else stays the same.

---

# Watch the data discovery

Even though we are using the same recipe, the source data available in each study area may differ.

As the Coos Bay workflow runs, watch the terminal output.

Ask:

1. Which sources are available in both Newport and Coos Bay?
2. Are the same NOAA hydrographic products available?
3. Is the density of terrestrial elevation data similar?
4. Does the topobathymetric lidar cover the same kinds of environments?
5. Are there areas with much sparser bathymetric measurements?
6. Does the workflow need more interpolation in one region than the other?

This demonstrates an important distinction:

> **The recipe can stay consistent even when the underlying data coverage changes.**

---

# Reuse relevant local data

Our workshop recipe adds:

```text
dav:survey_id=9693,weight=100
```

to the standard bundle.

This topobathymetric lidar survey covers both workshop regions, so it is appropriate to keep it in both builds.

Conceptually:

```text
coupling-bathy-topo
        +
dav survey 9693
        ↓
Newport DEM
```

and:

```text
coupling-bathy-topo
        +
dav survey 9693
        ↓
Coos Bay DEM
```

If we later moved to a region outside the footprint of survey 9693, we would no longer include it.

Instead, we could:

- use the standard bundle by itself
- add another locally appropriate source
- change source options or weights
- remove a predefined source when needed

The goal is:

> **Reuse high-quality local observations wherever they are spatially and scientifically relevant.**

---

# A bundle is a starting point

The `coupling-bathy-topo` bundle gives us a standardized starting recipe.

It does not lock us into a fixed set of inputs.

We can add sources directly:

```text
dav:survey_id=9693,weight=100
```

and we can also dynamically remove a predefined source before the recipe runs.

For example:

```bash
--modifier exclude_module:modules=MODULE_NAME
```

This gives us a useful balance:

> **Make the common workflow simple, while preserving the ability to apply local scientific judgment.**

---

# Reproducibility does not mean identical data coverage

A reusable workflow does not guarantee that every study area contains the same amount or quality of source data.

One region may contain:

- dense topobathymetric lidar
- recent hydrographic surveys
- extensive terrestrial lidar

while another may rely more heavily on:

- sparse hydrographic measurements
- supplemental bathymetry
- interpolation between observations

Those differences are scientifically meaningful.

The workflow should help us **identify and document those differences**, rather than hide them.

---

# Inspect the Coos Bay DEM

When the workflow finishes, look inside:

```text
coos_dem/
```

for the resulting DEM and supporting products.

As you explore the Coos Bay result, compare it with Newport.

Look for:

- coastline behavior
- topographic detail
- bathymetric morphology
- areas of dense measured coverage
- areas of sparse bathymetric coverage
- transitions between measured and interpolated terrain
- differences in which source datasets contribute

Ask yourself:

> **Does the same recipe behave reasonably in both regions?**

and:

> **What differences are caused by the data rather than by the workflow itself?**

---

# The complete workflow

Across the four workshop modules, we have now followed the full coastal DEM process:

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

make it possible to capture commonly useful data sources and processing choices in a reusable recipe.

## 3. Add local knowledge when it improves the result

For both workshop study areas, we enhance the standard bundle with:

```text
dav:survey_id=9693,weight=100
```

because that high-quality topobathymetric lidar survey is relevant to both regions.

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

The most important outcome is not a single Newport or Coos Bay DEM.

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
