---
title: "5 - Evaluate the DEMs with IVERT"
---

# Evaluate the DEMs with IVERT

We now have:

- a completed **Newport, Oregon** DEM
- a **Sarasota, Florida** DEM building in the background

Across the workshop, we have already:

- discovered and selected source elevation data
- transformed data into a common reference system
- filtered, prioritized, and gridded measurements
- interpolated the surface where measurements were sparse
- inspected the finished DEM and its spatial metadata
- transferred the same workflow to a second coastal region

Now we will ask an important final question:

> **After all of that processing, does the finished DEM agree with independent observations?**

For that, we will use **IVERT — the ICESat-2 Validation of Elevations Reporting Tool**.

IVERT compares a finished DEM with independent ICESat-2 observations and produces statistical and spatial evaluation products.

That makes IVERT especially valuable here because it provides an **independent end-to-end check on the result of the DEM-building workflow**.

Conceptually:

```text
source discovery
      ↓
transformations
      ↓
filtering + gridding + interpolation
      ↓
source prioritization
      ↓
finished DEM
      +
IVERT: independent ICESat-2 observations
      ↓
independent check on the resulting surface
```

IVERT does not independently test each processing step.

Instead, it asks whether the **combined result of those steps** agrees with independent observations where those observations are available.

:::{important}
## Two complementary checks

The spatial metadata and IVERT help us understand the DEM in different ways:

```text
Spatial metadata:
What source data and measurements support this surface?

IVERT:
Does the resulting surface agree with independent observations?
```

Together, they give us both **provenance** and an **independent check on the final result**.
:::

:::{dropdown} Why not just inspect the hillshade?

Visual inspection is extremely useful for finding obvious artifacts, discontinuities, or unrealistic terrain.

But a DEM can look reasonable and still contain vertical bias or other elevation differences.

IVERT adds an independent quantitative check that can reveal:

- systematic vertical bias
- the overall magnitude and spread of elevation differences
- outliers
- spatial patterns in the differences

Those patterns can help point us back toward source data or processing areas that deserve a closer look.

```text
visual inspection
        +
source-data provenance
        +
independent evaluation
        ↓
stronger understanding of the DEM
```

:::

---

# 1. Evaluate Newport first

While Sarasota continues building, we will begin with Newport.

Newport gives us our first **independent check of the finished DEM**.

We created this surface from multiple elevation sources and several processing steps. IVERT lets us compare the result with ICESat-2 observations that were not used to construct the DEM surface.

If the DEM agrees well with those observations, that gives us independent evidence that the **combined workflow is producing reasonable elevations in the sampled area**.

If IVERT reveals systematic or spatially organized differences, that gives us a useful signal that something in the source data or processing chain may deserve closer inspection.

---

## Get the Newport observations

Make sure the IVERT database contains the available independent observations for Newport:

```bash
ivert database download \
  -ds 2022.01.01 \
  -de 2024.11.06 \
  -- -124.10/-124.00/44.59/44.64
```

These observations may already be available in the workshop environment.

:::{important}
## Success check

The command should complete without an error and make the Newport ICESat-2 observations available to IVERT.

If the database has already been prepared for the workshop, IVERT may reuse existing data rather than downloading everything again.
:::

---

## Run the Newport comparison

Run:

```bash
ivert validate \
  newport_dem/newport_n44x64_w124x10_final.tif \
  -V navd88 \
  -n newport \
  -mc
```

| Option | Meaning |
|---|---|
| `newport_dem/newport_n44x64_w124x10_final.tif` | Newport DEM to evaluate |
| `-V navd88` | Vertical datum of the DEM |
| `-n newport` | Validation-run name |
| `-mc` | Generate the configured validation products |

IVERT compares the DEM elevations with the available independent ICESat-2 observations.

Depending on the configured outputs, IVERT can provide:

- number of validation observations
- mean vertical bias
- RMSE
- standard deviation and error percentiles
- validation plots
- spatial exports of elevation differences

:::{important}
## Success check

You should see IVERT begin processing the Newport DEM and the available ICESat-2 observations.

The result is an **independent check of the finished Newport surface**.
:::

---

## What can the Newport results tell us?

Look at the IVERT statistics and spatial outputs.

Ask:

1. Is the DEM systematically high or low relative to the independent observations?
2. How large are the elevation differences overall?
3. How much spread is there in those differences?
4. Are there isolated outliers?
5. Are differences spatially clustered in particular parts of the DEM?

These results can reveal behavior that may not be obvious from the hillshade alone.

For example:

```text
IVERT identifies a spatial pattern
        ↓
where does it occur?
        ↓
spatial metadata shows
what source supports that area
        ↓
inspect the source or processing context
        ↓
investigate a possible cause
```

IVERT identifies **where the finished result differs from independent observations**.

The spatial metadata helps us investigate **what went into the DEM at those locations**.

That combination makes the workflow much more transparent and diagnosable.

---

## Interpret the Newport results with their coverage

There is one important piece of context we need before generalizing the statistics:

> **Where are the independent observations located?**

For Newport, the available observations primarily sample the terrestrial/topographic portion of the DEM.

It tells us **which part of the DEM the independent check represents most directly**.

Inspect the validation observations spatially and ask:

1. How many observations are available?
2. Where are they located?
3. Are they primarily over land or water?
4. What part of the DEM do they sample?
5. Which source data support those locations?

:::{tip}
## Interpret results with their coverage

IVERT provides an independent check of the finished DEM where suitable observations are available.

When reporting a statistic such as bias or RMSE, describe the observation coverage along with the value.

The coverage tells us **where the independent check is strongest**.
:::

---

## Connect Newport IVERT results back to spatial metadata

Return mentally to the spatial metadata from Module 3.

Choose an area sampled by IVERT and ask:

> **Which source data support the DEM at this location?**

This connects:

```text
independent IVERT observation
        ↓
DEM difference
        ↓
spatial location
        ↓
source data identified in spatial metadata
        ↓
possible source / processing context
```

:::{important}
## One-location challenge

Choose one Newport IVERT validation location.

Can you trace:

```text
validation observation
        ↓
DEM elevation
        ↓
spatial metadata
        ↓
source supporting that location
```

One example is enough to demonstrate how **independent evaluation and provenance work together**.
:::

---

# 2. Return to Sarasota

Now check the terminal where the Sarasota build has been running.

First, see whether the final DEM has been created:

```bash
ls -lh sarasota_dem/*_final.tif
```

:::{important}
## Sarasota build check

If you see a Sarasota file ending in:

```text
_final.tif
```

the DEM is ready for IVERT.

If the build is still finishing, leave it running for another moment while we review the evaluation workflow.
:::

Sarasota gives us an especially useful coastal DEM evaluation case because the available independent ICESat-2 observations include **bathymetric observations**.

That lets IVERT provide an independent check on part of the **submerged coastal surface**, in addition to the terrestrial portion of the DEM.

---

## Get the Sarasota observations

Make sure the IVERT database contains the available independent observations for Sarasota:

```bash
ivert database download \
  -ds 2022.01.01 \
  -de 2024.11.06 \
  -- -82.59/-82.53/27.28/27.34
```

As with Newport, these observations may already be available in the workshop environment.

:::{important}
## Success check

The command should complete without an error and make the Sarasota ICESat-2 observations available to IVERT.

If they are already present, IVERT may reuse the existing database.
:::

---

## Run the Sarasota comparison

Once the Sarasota DEM is complete, run:

```bash
ivert validate \
  sarasota_dem/*_final.tif \
  -V navd88 \
  -n sarasota \
  -mc
```

| Option | Meaning |
|---|---|
| `sarasota_dem/*_final.tif` | Sarasota final DEM generated by Globato |
| `-V navd88` | Vertical datum of the DEM |
| `-n sarasota` | Validation-run name |
| `-mc` | Generate the configured validation products |

:::{important}
## Success check

You should see IVERT begin processing the Sarasota DEM and the available independent ICESat-2 observations.

This gives us a second **independent end-to-end check of the DEM-building workflow**, now in a different coastal environment.
:::

---

## What can the Sarasota results tell us?

Inspect the IVERT statistics and spatial outputs.

Ask the same questions we asked for Newport:

1. Is there an overall vertical bias?
2. How large are the elevation differences?
3. What is the spread of those differences?
4. Are there outliers?
5. Are the differences spatially organized?

But Sarasota gives us an additional opportunity:

> **What do the independent bathymetric observations tell us about the submerged portion of the finished coastal DEM?**

This is particularly valuable for a topobathymetric workflow because we can independently check elevations **below the water surface**, not only the terrestrial terrain.

:::{important}
## Compare land and water

Look at where the Sarasota IVERT observations occur.

Can you identify:

- terrestrial validation observations
- bathymetric validation observations
- portions of the DEM where those observations provide an independent check?

Consider whether the elevation differences behave similarly or differently across the terrestrial and submerged portions of the DEM.
:::

---

## Keep the Sarasota coverage in context

The bathymetric observations make the Sarasota example especially useful, but the same interpretation rule still applies:

> **The independent check is strongest where the observations actually sample the DEM.**

---

# 3. Compare what Newport and Sarasota teach us

Together, the two study areas show the value of IVERT across different parts of a coastal DEM.

| | Newport | Sarasota |
|---|---|---|
| **DEM type** | Coastal topobathymetric DEM | Coastal topobathymetric DEM |
| **Independent observations** | Primarily topographic | Includes topographic and bathymetric observations |
| **What IVERT adds** | Independent evidence about the finished Newport surface | Independent evidence across both land and submerged coastal terrain |
| **What we can investigate** | Bias, elevation differences, spread, outliers, spatial patterns | The same metrics, including bathymetric performance |
| **Interpretation context** | Results are strongest where observations sample the DEM | Results are strongest where topographic and bathymetric observations sample the DEM |

The main point is the same in both places:

> **IVERT provides an external check on the result of the complete DEM-building process.**

The observation coverage tells us **where that check applies most directly**.

Conceptually:

```text
finished DEM
      +
independent observations
      ↓
IVERT
      ↓
bias + error statistics + spatial patterns
      ↓
independent evidence about DEM performance
      ↓
connect patterns back to provenance when needed
```

:::{important}
## The IVERT takeaway

IVERT gives us something we did not have during the DEM build:

> **an independent check on the result of the entire workflow.**

It can reveal bias, elevation differences, spatial patterns, and potential problems that may not be apparent from the DEM alone.

To interpret those results responsibly, we also consider:

1. where the independent observations are located
2. what part of the DEM they sample
3. whether they are independent of the DEM inputs
4. how the observed differences relate back to the DEM's source data and processing

:::

---

# The pattern we used today

Across the workshop, we followed the same general workflow:

```text
define the study area
        ↓
start with a national coastal data recipe
        ↓
look for useful local data
        ↓
add local data to the recipe
        ↓
build the coastal DEM
        ↓
inspect the finished surface
        ↓
inspect source-data provenance
        ↓
adapt the workflow to a new region
        ↓
build another coastal DEM
        ↓
independently evaluate the finished DEMs
```

The last step is important.

We do not stop when the DEM looks good.

We use independent observations to ask whether the **result of the complete workflow** is behaving as expected.

---

# Final takeaway

The most important result of the workshop is not a single Newport or Sarasota DEM.

It is the framework:

> **Start with a reusable national-scale recipe, add locally appropriate data, keep the workflow transparent enough to inspect, transfer it to new regions, and independently evaluate the finished DEM.**

In short:

```text
build transparently
      ↓
understand provenance
      ↓
reuse the workflow
      ↓
independently check the result
```

:::{tip}
## Try your own study area

For another U.S. coastal region:

1. Define your bounds.
2. Start with `coupling-bathy-topo`.
3. Search NOAA Digital Coast DAV for useful local lidar.
4. Add the appropriate dataset when it improves the recipe.
5. Build the DEM.
6. Inspect the finished surface.
7. Inspect where the elevations came from.
8. Evaluate the finished DEM with appropriate independent observations.
9. Interpret the evaluation together with its spatial coverage.

That is the same workflow we used for Newport and Sarasota.
:::

---

# One final question

If you applied this workflow to your own region:

> **What local dataset would you want to add first?**

# Want to learn more? Join the Continuous-DEMs community!

Have a dataset we should support?  
Want to request a feature, suggest a new method, or ask a question?

<p>
  <a href="https://cudem.zulipchat.com/">
    <img src="../../assets/images/zulip-icon-circle.svg" alt="" width="22" height="22" style="vertical-align: -0.28em; margin-right: 0.35em;">
    <strong>Join the CUDEM Zulip community</strong>
  </a>
</p>

We would love to hear how you are using the tools and what would make them more useful.

## Want to contribute code?

Continuous-DEMs is proudly open source, and we would love community contributions!

<p>
  <a href="https://github.com/continuous-dems">
    <img src="../../assets/images/mark-github-24.svg" alt="" width="22" height="22" style="vertical-align: -0.28em; margin-right: 0.35em;">
    <strong>Explore Continuous-DEMs on GitHub</strong>
  </a>
</p>

If you are new to contributing to an open-source project, we are happy to help walk you through the process!
