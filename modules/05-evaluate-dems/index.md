---
title: "5 - Evaluate the DEMs with IVERT"
---

# Evaluate the DEMs with IVERT

We now have:

- a completed **Newport, Oregon** DEM
- a **Sarasota, Florida** DEM building in the background

So far, we have focused on:

- how the DEMs are built
- what source data support the finished surfaces
- how the same recipe can be transferred to a new coastal region

Now we will ask a different question:

> **How do the finished DEMs compare with independent elevation observations?**

For that, we will use **IVERT**.

IVERT provides a repeatable way to compare a DEM with independent observations and generate statistical and spatial evaluation products.

Conceptually:

```text
finished DEM
      +
independent observations
      ↓
elevation differences
      ↓
statistics + spatial quality-control products
      ↓
better understanding of DEM performance
```

:::{important}
## Two different questions

The spatial metadata and IVERT tell us different things:

```text
Spatial metadata:
What source data support the DEM?

IVERT:
How does the finished DEM compare with independent observations?
```

Both are important for understanding a DEM.
:::

:::{dropdown} Why not just inspect the hillshade?

Visual inspection is extremely useful for finding obvious artifacts, discontinuities, or unrealistic terrain.

But a DEM can look reasonable and still contain vertical bias or other elevation differences.

IVERT complements visual inspection by adding an independent, quantitative comparison.

```text
visual inspection
        +
source-data provenance
        +
independent evaluation
        ↓
stronger DEM understanding
```

:::

---

# 1. Evaluate Newport first

While Sarasota continues building, we will begin with Newport.

The Newport example is especially useful for learning an important rule:

> **Always look at where the validation observations occur before interpreting the statistics.**

The available Newport observations primarily sample the terrestrial/topographic portion of the DEM.

That means the results tell us much more about that sampled portion than about the entire coastal DEM.

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

The command should complete without an error and make the Newport observations available to IVERT.

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

IVERT compares the DEM elevations with the available independent observations and creates statistical and spatial evaluation products.

:::{important}
## Success check

You should see IVERT begin processing the Newport DEM and the available validation observations.

**Do not interpret the summary statistics yet.**

First, inspect where the observations are located.
:::

---

## Look at Newport validation coverage first

Before focusing on RMSE or another summary statistic, inspect the validation observations spatially.

Ask:

1. How many observations are available?
2. Where are they located?
3. Are they primarily on land or in the water?
4. What part of the DEM do they sample?
5. Which source data support those locations?

:::{tip}
## Coverage gives the statistics meaning

A validation statistic is not automatically a measure of the entire DEM.

It summarizes the part of the surface sampled by the available independent observations.

For Newport, those observations primarily sample the terrestrial/topographic domain.
:::

---

## Now look at the Newport elevation differences

Inspect the available IVERT comparison statistics.

Useful quantities may include:

- number of validation observations
- mean elevation difference or bias
- RMSE
- spread of elevation differences

:::{important}
## Your turn: write one defensible sentence

Complete:

> **For the available Newport validation observations, the DEM __________.**

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

## Connect Newport validation back to spatial metadata

Return mentally to the spatial metadata from Module 3.

Ask:

> **Which source data support the locations sampled by the IVERT observations?**

This connects:

```text
independent observations
        ↓
sampled part of DEM
        ↓
source data identified in spatial metadata
        ↓
comparison statistics
        ↓
scientific interpretation
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
```

You do not need to do this for every point.

One example is enough to demonstrate the reasoning process.
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

The Sarasota example gives us a useful contrast with Newport because the available independent observations include **bathymetric validation**.

That lets us evaluate part of the submerged coastal surface directly rather than relying only on terrestrial observations.

---

## Get the Sarasota observations

Make sure the IVERT database contains the available independent observations for the Sarasota study area:

```bash
ivert database download \
  -ds 2022.01.01 \
  -de 2024.11.06 \
  -- -82.59/-82.53/27.28/27.34
```

As with Newport, these observations may already be available in the workshop environment.

:::{important}
## Success check

The command should complete without an error and make the Sarasota observations available to IVERT.

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

You should see IVERT begin processing the Sarasota DEM and the available independent observations.
:::

---

## Look at Sarasota validation coverage first

Use the same rule we used for Newport:

> **Look at the observations before interpreting the statistics.**

This time, pay particular attention to the observations in the water.

Ask:

1. Where are the bathymetric validation observations?
2. What part of the Sarasota DEM do they sample?
3. Are there also terrestrial observations?
4. Do the observations span areas supported by different DEM source datasets?
5. What parts of the coastal DEM are still not independently sampled?

:::{tip}
The presence of bathymetric validation observations gives us a more direct way to evaluate the submerged portion of the Sarasota coastal DEM.

It still does **not** mean that every bathymetric cell has been independently validated.

The interpretation should remain tied to where the observations actually occur.
:::

---

## Look at the Sarasota elevation differences

Inspect the IVERT statistics and spatial outputs for Sarasota.

Again, useful quantities may include:

- number of validation observations
- mean elevation difference or bias
- RMSE
- spread of elevation differences

Now ask a slightly different question:

> **What do the independent bathymetric observations tell us about the submerged portion of the Sarasota DEM?**

:::{important}
## Your turn: write one defensible Sarasota sentence

Complete:

> **For the available Sarasota bathymetric validation observations, the DEM __________.**

Then complete:

> **These results describe __________.**

Keep the conclusion tied to the spatial coverage of the independent observations.
:::

---

# 3. Compare what Newport and Sarasota teach us

The two examples illustrate why independent evaluation must be interpreted spatially.

| | Newport | Sarasota |
|---|---|---|
| DEM type | Coastal topobathymetric DEM | Coastal topobathymetric DEM |
| Independent observations | Only topographic | Both topographic and bathymetric observations |
| Main lesson | Validation statistics describe the sampled topography | Validation statistics describe the sampled topography and bathymetry |
| Important caution | Do not generalize topographic validation to the entire coastal DEM | Still do not generalize topographic and bathymetric validation to the entire coastal DEM |

The scientific workflow is the same in both places:

```text
independent observations
        ↓
where are they?
        ↓
what part of the DEM do they sample?
        ↓
what source data support that area?
        ↓
what do the statistics show?
        ↓
what conclusion is justified?
```

:::{important}
## The evaluation takeaway

A validation statistic only becomes scientifically meaningful when we understand:

1. **where the independent observations are**
2. **what part of the DEM they sample**
3. **whether they are independent of the DEM inputs**
4. **what conclusion that coverage actually supports**
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
evaluate with appropriate independent observations
```

---

# Final takeaway

The most important result of the workshop is not a single Newport or Sarasota DEM.

It is the framework:

> **Start with a reusable national-scale recipe, add locally appropriate data, keep the workflow transparent enough to inspect, and evaluate the finished DEM with appropriate independent observations.**

:::{tip}
## Try your own study area

For another U.S. coastal region:

1. Define your bounds.
2. Start with `coupling-bathy-topo`.
3. Search NOAA Digital Coast DAV for useful local lidar.
4. Add the appropriate dataset when it improves the recipe.
5. Build the DEM.
6. Inspect where the elevations came from.
7. Evaluate the result using appropriate independent observations.

That is the same workflow we used for both Newport and Sarasota.
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
