---
title: "3 - Explore and Evaluate the DEM"
---

# Explore and Evaluate the DEM

We now have a coastal DEM for **Newport, Oregon**.

In this module, we will move from **building** the DEM to **understanding** it.

Our workflow is:

```text
inspect the DEM
      ↓
inspect source provenance
      ↓
look at independent validation coverage
      ↓
compare the DEM with those observations
      ↓
interpret the results in context
```

The key question is:

> **What part of the DEM are our validation observations actually testing?**

---

# 1. Check the Newport outputs

The two primary workshop products are:

```text
newport_cudem/newport_final.tif
newport_cudem/newport_hs.tif
```

If your build has finished, run:

```bash
ls -lh newport_cudem/newport_final.tif newport_cudem/newport_hs.tif
```

:::{important}
## Success check

You should see both files listed:

```text
newport_final.tif
newport_hs.tif
```

If your build did not finish, use the instructor-provided Newport outputs and continue with the group.
:::

<!-- TODO / RESCUE PATH PLACEHOLDER
Add the exact shared path for instructor-provided Newport outputs.

Suggested wording:

"If your build is still running or failed, use:

[EXACT SHARED NEWPORT OUTPUT PATH]

for the remainder of Module 3."
-->

---

# 2. Look at the finished DEM

Open:

```text
newport_cudem/newport_final.tif
newport_cudem/newport_hs.tif
```

Use the hillshade first to get oriented.

Look at:

- Yaquina Bay
- the entrance channel
- the coastline
- adjacent terrestrial terrain
- nearshore bathymetry
- transitions between detailed and smoother areas

<!-- IMAGE PLACEHOLDER
Highest-value visual for this section:

Side-by-side:
1. Newport DEM
2. Newport hillshade

Ideally use the same map extent and include a small locator or coastline outline.

Suggested caption:
"The finished Newport coastal DEM and hillshade."
-->

:::{important}
## Your turn: pick one place

Choose one feature or area that catches your attention.

For example:

- a detailed topographic feature
- the shoreline
- the entrance channel
- an area of smooth bathymetry
- a transition between different-looking parts of the DEM

Keep that location in mind.

We will try to determine **what source data support it**.
:::

---

# 3. Where did those elevations come from?

A finished DEM should not be a black box.

Look inside:

```text
newport_cudem/
```

Run:

```bash
ls -lh newport_cudem/
```

The directory contains the finished products plus supporting information created during the build.

Exact filenames may evolve, but the scientific question stays the same:

> **What observations support the DEM at a particular location?**

<!-- IMAGE PLACEHOLDER
Very high-value figure:

A Newport source/provenance visualization showing which parts of the
study area are supported by different source datasets.

Ideally use the same extent as the DEM/hillshade figure.

Possible legend categories:
- local topobathymetric lidar
- national terrestrial elevation
- hydrographic observations
- other bathymetric sources
- interpolation / no direct measurement

Use the actual Globato provenance/source product once its final filename
and symbology are confirmed.

Suggested caption:
"Source provenance helps connect the finished DEM back to the observations used to build it."
-->

:::{important}
## Your turn: trace one location

Return to the feature you picked above.

Ask:

1. Is this location supported by a direct measurement?
2. Which source appears to contribute there?
3. Is the surrounding surface measurement-dense or more dependent on interpolation?

You do not need to classify the whole DEM.

Just trace **one location** from the finished surface back to its source support.
:::

---

# 4. Explore the tools directly

Globato coordinated the build, but the individual tools can also be inspected directly.

## Fetchez

List the available data modules:

```bash
fetchez modules list
```

Inspect the coastal bundle again:

```bash
fetchez modules bundles info coupling-bathy-topo
```

If a particular source interests you:

```bash
fetchez modules info MODULE_NAME
```

Recall that we also added the Newport DAV lidar directly:

```text
dav:survey_id=9693,weight=100
```

So the source recipe was:

```text
national coastal data
        +
local Newport topobathymetric lidar
```

---

## Transformez

List the available transformations:

```bash
transformez list
```

Our Newport DEM was requested in:

```text
epsg:4269+5703
```

or:

```text
NAD83 + NAVD88
```

:::{dropdown} Why inspect the individual tools?

The workshop uses Globato to coordinate the complete workflow, but the framework is modular.

Exploring Fetchez and Transformez separately helps show that:

- data access is its own capability
- reference-system transformation is its own capability
- Globato combines those capabilities into a DEM-building recipe

That modularity makes it easier to reuse or extend parts of the workflow later.
:::

---

# 5. What do the available validation observations sample?

Next we will use **IVERT** to compare the DEM with independent ICESat-2 observations.

Before calculating or interpreting any statistics, we need to understand the validation coverage.

In this small Newport workshop area, the available observations primarily sample the **topographic portion** of the DEM.

That makes this a useful focused example of the validation workflow.

<!-- IMAGE PLACEHOLDER
Highest-value IVERT figure:

Map showing the Newport DEM or hillshade with the available IVERT / ICESat-2
validation observations overlaid.

This figure should make it visually obvious that the available observations
are primarily terrestrial/topographic.

Suggested caption:
"Available independent IVERT observations in the Newport workshop area primarily sample the topographic portion of the DEM."
-->

:::{important}
## Predict the interpretation, not the error

Before running the comparison, look at the validation-coverage map.

Ask:

> **What part of the Newport DEM will these observations actually evaluate?**

Do not try to predict where the largest errors will be.

Focus on the **domain being sampled**.
:::

:::{dropdown} Check your interpretation

For this workshop area, the available observations primarily provide an independent evaluation of the **topographic portion** of the DEM.

They do not provide equivalent independent coverage of:

- Yaquina Bay bathymetry
- offshore bathymetry
- most submerged topobathymetric lidar
- the entire land-to-seafloor DEM

That is a limitation of the **available observations in this small AOI**, not of the IVERT workflow itself.
:::

---

# 6. Get the independent observations

Make sure the IVERT database contains the available ICESat-2 observations for Newport:

```bash
ivert database download \
  -ds 2022.01.01 \
  -de 2024.11.06 \
  -- -124.1/-124/44.59/44.64
```

These observations may already be available in the workshop environment.

:::{important}
## Success check

The command should complete without an error and make the Newport observations available to IVERT.

If the database has already been prepared for the workshop, IVERT may reuse existing data rather than downloading everything again.
:::

<!-- OPTIONAL TERMINAL SCREENSHOT PLACEHOLDER
Add a short example of a successful IVERT database download/reuse message.

Do not include a long terminal dump.
-->

---

# 7. Run the IVERT comparison

Run:

```bash
ivert validate \
  newport_cudem/newport_final.tif \
  -V navd88 \
  -n newport \
  -mc
```

| Option | Meaning |
|---|---|
| `newport_cudem/newport_final.tif` | DEM to evaluate |
| `-V navd88` | Vertical datum of the DEM |
| `-n newport` | Validation-run name |
| `-mc` | Generate the configured validation products |

IVERT compares the DEM elevations with the available independent observations and creates statistical and spatial quality-control products.

:::{important}
## Success check

You should see IVERT begin processing the Newport DEM and the available validation observations.

Do not interpret the summary statistics yet.

First, inspect **where the observations are located**.
:::

<!-- TODO / OUTPUT PLACEHOLDER
Once the exact IVERT output filenames are confirmed, add a short command here
that lists only the 2–4 products participants actually need.

Example placeholder:

```bash
ls -lh [IVERT_OUTPUT_DIRECTORY]
```

Avoid making participants inspect a large output tree.
-->

---

# 8. Look at validation coverage first

Before focusing on RMSE or any other summary statistic, inspect the validation observations spatially.

Ask:

1. How many observations are available?
2. Where are they located?
3. Are they primarily on land or in the water?
4. What part of the DEM do they sample?
5. Which source data support those locations?

<!-- IMAGE PLACEHOLDER
If IVERT produces a map/plot showing validation locations, insert the actual
workshop output here.

If possible, pair it with the source/provenance map using the same extent.

Suggested caption:
"Validation statistics should be interpreted together with the spatial distribution of the independent observations."
-->

:::{tip}
## Coverage gives the statistics meaning

A validation statistic is not automatically a measure of the entire DEM.

It summarizes the part of the surface sampled by the available independent observations.

For Newport, those observations primarily sample the terrestrial/topographic domain.
:::

---

# 9. Now look at the elevation differences

Inspect the available IVERT comparison statistics.

Depending on the output, useful quantities may include:

- number of validation observations
- mean elevation difference or bias
- RMSE
- spread of elevation differences

<!-- IMAGE PLACEHOLDER
Suggested figure:
A compact screenshot of the actual IVERT summary output or primary
comparison plot used during the workshop.

Do not create a new statistical graphic just for the website if IVERT already
produces an appropriate one.

Suggested caption:
"IVERT summary statistics for the available Newport validation observations."
-->

:::{important}
## Your turn: write one defensible sentence

Complete:

> **For the available topographic validation observations, the Newport DEM __________.**

Then complete:

> **These results primarily describe __________.**

The goal is to make a statement supported by both the statistics **and** the observation coverage.
:::

:::{dropdown} What should we avoid saying?

Avoid conclusions such as:

> "The RMSE represents the accuracy of the entire Newport coastal DEM."

The available observations do not sample the entire coastal domain.

A better interpretation is:

> "For the available independent observations, which primarily sample the topographic portion of the Newport DEM, the comparison shows ..."

That keeps the conclusion aligned with the evidence.
:::

---

# 10. Connect validation back to source provenance

Much of the terrestrial portion of the Newport DEM is supported by the USGS national elevation data included in our standard coastal recipe.

Return to the source/provenance information.

Ask:

> **Which source data support the locations sampled by the IVERT observations?**

This connects:

```text
independent observations
        ↓
sampled part of DEM
        ↓
source data supporting that area
        ↓
comparison statistics
        ↓
scientific interpretation
```

:::{important}
## One-location challenge

Choose one IVERT validation location.

Can you trace:

```text
validation observation
        ↓
DEM elevation
        ↓
source/provenance
```

You do not need to do this for every point.

One example is enough to demonstrate the reasoning process.
:::

---

# 11. What would we need to evaluate the bathymetry?

If we wanted to independently evaluate the bathymetric portion of the Newport DEM, we would need appropriate independent observations in the water.

Examples could include:

- independent hydrographic soundings
- withheld sonar observations
- independent bathymetric lidar
- other appropriate seafloor measurements not used to build the DEM

The validation data should match the **domain we want to evaluate**.

:::{tip}
A useful general rule is:

```text
Where are the validation observations?
        ↓
What part of the DEM do they sample?
        ↓
Are they independent of the DEM inputs?
        ↓
What conclusion is justified?
```
:::

---

# What have we learned?

At this point, we have:

- inspected the finished Newport DEM
- traced part of the surface back to its source data
- explored Fetchez and Transformez directly
- used IVERT for independent DEM evaluation
- inspected validation coverage before interpreting statistics
- recognized that the available Newport observations primarily sample the topographic portion
- connected the independent observations back to the source data they evaluate

The final workshop question is:

> **Can we reuse the same workflow in another coastal region with different local data?**

Next:

> **Module 4 — Same Recipe, New Region**

We will move to **Sarasota, Florida**.
