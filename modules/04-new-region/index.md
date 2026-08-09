---
title: "4 - Same Recipe, New Region"
---

# Same Recipe, New Region

We have now built, inspected, and evaluated a coastal DEM for Newport, Oregon.

The final question is:

> **How much of that workflow needs to change if we want to build a coastal DEM somewhere else?**

One of the main goals of the Continuous DEMs project is to make coastal DEM generation **reusable and reproducible**.

Instead of rebuilding a new workflow from scratch for every location, we can reuse the same standardized recipe and change only the parts that are specific to the new study area.

---

# What stays the same?

For a new coastal region, much of the workflow can remain unchanged:

```text
coupling-bathy-topo
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
final DEM
        ↓
provenance and validation
```

The same bundle can search for the configured source types wherever they are available.

The workflow then determines which datasets actually overlap the requested region.

---

# What changes?

At minimum, we need a new region of interest.

For Newport, we used:

```text
-R -124.1/-124/44.59/44.64
```

For another study area, replace that region with the new:

```text
WEST/EAST/SOUTH/NORTH
```

We should also give the new project its own output name and directory.

For example:

```bash
globato build \
  -R WEST/EAST/SOUTH/NORTH \
  -E .11111111s \
  -P epsg:4269+5703 \
  -X 6:5 \
  -O second_area \
  -D second_area_cudem \
  --shared-cache coupling-shared-dir \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

:::{important}
Use the second study-area bounds provided by the instructors during the workshop.

The second region should be tested in advance so that the available source data and runtime are appropriate for the live exercise.
:::

---

# Reuse relevant local data

Our Newport command included:

```text
dav:survey_id=9693,weight=100
```

The second workshop region is also covered by this topobathymetric lidar survey, so we should keep it in the recipe.

This illustrates an important point:

> **Location-specific data should be included wherever they are spatially and scientifically relevant.**

The recipe for both workshop regions is therefore:

```text
coupling-bathy-topo
        +
dav:survey_id=9693,weight=100
```

If we later move to a region outside this survey footprint, we would remove it and add any other locally appropriate datasets instead.

The goal is not to carry every local dataset everywhere. The goal is to reuse high-quality local observations wherever they actually apply.

---

# A bundle is a starting point

The `coupling-bathy-topo` bundle provides a standardized starting recipe.

It does not lock us into a fixed set of inputs.

We can:

- add another data source
- change source options
- adjust source weights
- remove a predefined source when it is not appropriate

For example, a module can be dynamically excluded before the recipe runs:

```bash
--modifier exclude_module:modules=MODULE_NAME
```

This gives us a useful balance:

> **Make the common workflow simple, while preserving the ability to apply local scientific judgment.**

---

# Run the second study area

Once the instructors provide the second region, run:

```bash
globato build \
  -R WEST/EAST/SOUTH/NORTH \
  -E .11111111s \
  -P epsg:4269+5703 \
  -X 6:5 \
  -O second_area \
  -D second_area_cudem \
  --shared-cache coupling-shared-dir \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

While it runs, compare the terminal output with the Newport build.

Ask:

1. Which datasets are available in both regions?
2. Which sources are available only in one region?
3. Does the same preprocessing logic apply?
4. Are the data densities similar?
5. Does the workflow need more interpolation in the new region?
6. Is there a local dataset that should be added to improve the standard recipe?

---

# Compare the two DEMs

The goal is not for the two study areas to contain identical source data.

The goal is for them to use the same **reproducible workflow logic**.

Conceptually:

```text
Newport ROI
    +
coupling-bathy-topo
    +
Newport-specific lidar
        ↓
Newport DEM
```

and:

```text
New ROI
    +
coupling-bathy-topo
    +
dav survey 9693
        ↓
New coastal DEM
```

Because both workshop regions intersect the same high-quality topobathymetric lidar survey, that local enhancement remains part of both builds.

The available measurements may differ substantially between locations, but the process used to discover, prepare, combine, document, and evaluate them remains consistent.

---

# Reproducibility does not mean identical data coverage

A standardized workflow does not guarantee that every location has the same quantity or quality of source data.

One region may contain:

- dense topobathymetric lidar
- recent hydrographic surveys
- extensive terrestrial lidar

while another may rely more heavily on:

- sparse hydrographic measurements
- lower-priority supplemental bathymetry
- interpolation between observations

That difference is scientifically meaningful.

The workflow should help us **see and document those differences**, rather than hide them.

---

# The complete workflow

Across the four workshop modules, we have followed the full coastal DEM process:

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
reuse and refine the workflow
```

---

# Workshop takeaways

The main ideas from today's workshop are:

## 1. Start with the scientific problem

The goal is not to run a particular software package.

The goal is to build a defensible coastal elevation model from the best available observations.

## 2. Standardize the common workflow

Bundles such as:

```text
coupling-bathy-topo
```

make it possible to capture commonly useful data sources and processing choices in a reusable recipe.

## 3. Keep local knowledge easy to add

A general recipe will never contain every useful local dataset.

For the workshop regions, we improve the standard recipe by adding:

```text
dav:survey_id=9693,weight=100
```

Both regions are covered by this survey, so the same high-quality local source is retained in each build. The same principle applies elsewhere: add local data wherever they are actually relevant.

## 4. Treat source datasets appropriately

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

Multiresolution processing helps bring those different data densities together in one continuous surface.

## 6. Keep provenance

A DEM should not be a black box.

We should be able to determine where the input data came from and how they contributed to the final surface.

## 7. Validate independently

Visual inspection is necessary but not sufficient.

Independent observations such as ICESat-2 provide another way to evaluate the finished DEM and identify areas that may need further investigation.

## 8. Reuse the workflow

The most important outcome is not a single Newport DEM.

It is a workflow that can be applied, inspected, evaluated, and refined for other coastal regions.

---

:::{tip}
## Where to go next

Try a region that matters to your own work.

Start with the standard bundle, inspect which data are available, and then decide whether local datasets or recipe changes would improve the result.

The same workflow can become the starting point for a new coastal DEM rather than a one-off exercise.
:::

---

# Questions and discussion

What would you want to change for your own study area?

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

