---
title: "3 - Explore and Evaluate the DEM"
---

# Explore and Evaluate the DEM

We now have a finished coastal DEM for Newport, Oregon.

In this module, we will inspect the DEM, look at where its elevations came from, and use IVERT to evaluate it with independent ICESat-2 observations.

Our workflow is:

```text
inspect the DEM
      ↓
inspect source provenance
      ↓
look at the available validation observations
      ↓
evaluate the sampled portion of the DEM
      ↓
interpret the results in context
```

> **The goal is not just to calculate an accuracy statistic. It is to understand what the validation observations are actually telling us about the DEM.**

---

# 1. Explore the finished DEM

Open:

```text
newport_cudem/newport_final.tif
newport_cudem/newport_hs.tif
```

Spend a minute exploring the surface.

Look at:

- Yaquina Bay
- the entrance channel
- the coastline
- adjacent terrestrial terrain
- nearshore bathymetry
- transitions between detailed and smoother areas

:::{important}
## Pick one place to inspect

Choose one feature that catches your attention.

It might be:

- a detailed topographic feature
- the shoreline
- the entrance channel
- an area of smooth bathymetry
- a transition between different-looking parts of the DEM

Keep it in mind as we look at the source data.
:::

---

# 2. Where did the DEM elevations come from?

A finished DEM should not be a black box.

Look inside:

```text
newport_cudem/
```

Globato creates supporting information that can help us determine:

- which source datasets were used
- where different sources contributed
- where sources overlapped
- where interpolation played a larger role

Exact filenames may evolve, but the scientific question stays the same:

> **What observations support the DEM at a particular location?**

## Your turn: trace one source

Try to identify at least one area supported by:

- the Newport topobathymetric lidar
- NOAA hydrographic data
- terrestrial elevation data
- interpolation between measurements

Then return to the feature you picked above.

> **What appears to support the DEM there?**

---

# 3. Look under the hood

Globato coordinated the complete build, but the underlying tools can also be used directly:

```text
Fetchez      → data discovery and access
Transformez  → reference-system transformations
Globato      → DEM generation
IVERT        → independent DEM evaluation
```

## Explore Fetchez

List the available data modules:

```bash
fetchez modules list
```

Choose one source that interests you and inspect it:

```bash
fetchez modules info MODULE_NAME
```

Now inspect the national coastal bundle used for Newport:

```bash
fetchez modules bundles info coupling-bathy-topo
```

Recall that we added the locally selected Digital Coast survey:

```text
dav:survey_id=9693,weight=100
```

So the Newport recipe is:

```text
national coastal data
        +
local Newport topobathymetric lidar
```

---

## Explore Transformez

List the available transformations:

```bash
transformez list
```

Our Newport DEM is requested in:

```text
epsg:4269+5703
```

or:

```text
NAD83 + NAVD88
```

Source datasets may begin in other horizontal or vertical reference systems, so they must be standardized before they are combined.

:::{dropdown} Why does the vertical datum matter?

The same physical location can have different numerical elevation values when referenced to different vertical surfaces.

A vertical transformation changes the **reference used to describe the elevation**. It does not change the physical terrain or seafloor.

That standardization is essential before elevations from different sources can be stacked together.
:::

---

# 4. What do the available validation observations sample?

IVERT provides an independent way to evaluate the DEM using available ICESat-2 observations.

In this small Newport workshop area, the available observations primarily sample the **topographic portion** of the DEM.

That makes this a useful focused example of the validation workflow.

:::{important}
## Look at the validation coverage first

Before interpreting any statistics, ask:

- Where are the independent observations located?
- Are they on land or in the water?
- Which part of the DEM are they sampling?

The spatial coverage of the observations tells us how to interpret the validation results.
:::

:::{dropdown} What does that mean for Newport?

For this workshop area, the available IVERT observations primarily provide an independent check of the **topographic portion** of the DEM.

Because the observations are concentrated on land, the resulting statistics mainly describe that sampled topographic domain.

Other independent observations would be needed to evaluate the bathymetric portion with the same level of confidence.
:::

---

# 5. Get the independent observations

Make sure the IVERT database contains the available ICESat-2 observations for Newport:

```bash
ivert database download \
  -ds 2022.01.01 \
  -de 2024.11.06 \
  -- -124.1/-124/44.59/44.64
```

These observations may already be available in the workshop environment.

---

# 6. Run the IVERT comparison

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

IVERT compares the DEM elevations with the available independent observations and produces statistical and spatial quality-control products.

---

# 7. Inspect the validation coverage

Before focusing on RMSE or other summary statistics, look at where the validation observations occur.

Ask:

1. How many observations are available?
2. Where are they located?
3. What part of the DEM do they sample?

:::{tip}
## Coverage gives the statistics context

Validation statistics are most useful when interpreted together with the location and type of the independent observations.

Here, the available observations primarily sample the terrestrial portion of the Newport DEM.
:::

---

# 8. Look at the topographic comparison

Now inspect the available elevation-difference statistics.

Depending on the IVERT output, these may include:

- number of validation observations
- mean elevation difference or bias
- RMSE
- spread of elevation differences

These provide an independent evaluation of the portion of the DEM sampled by the available ICESat-2 observations.

## Your turn: summarize the result

Complete:

> **For the available topographic validation observations, the Newport DEM __________.**

Then add:

> **These observations primarily evaluate __________.**

This keeps the interpretation tied directly to the validation coverage.

---

# 9. Connect validation back to the source data

Much of the topographic portion of the Newport DEM is supported by the USGS national elevation data included in our standard recipe.

Return to the Globato source/provenance products.

Ask:

> **Which source data support the locations sampled by the IVERT observations?**

This helps connect the independent evaluation back to the data that contributed to the DEM.

:::{tip}
A useful validation workflow is:

```text
Where are the independent observations?
        ↓
What part of the DEM do they sample?
        ↓
Which source data support that area?
        ↓
What do the comparison statistics tell us?
```

IVERT makes it possible to connect independent observations with the DEM in a repeatable evaluation workflow.
:::

---

# 10. What would we need to evaluate the bathymetry?

If we wanted to independently evaluate the bathymetric portion of the Newport DEM, we would need appropriate independent observations in the water.

Examples might include:

- independent hydrographic soundings
- withheld sonar observations
- independent bathymetric lidar
- other appropriate seafloor measurements not used to build the DEM

The validation observations should match the **domain we want to evaluate**.

That leads to an important workshop takeaway:

> **Validation is most informative when we interpret the results in the context of the observations that are available.**

---

# What have we learned?

At this point, we have:

- inspected the finished Newport DEM
- connected the surface back to its source data
- explored Fetchez and Transformez directly
- used IVERT for independent DEM evaluation
- inspected the validation coverage before interpreting statistics
- recognized that the available observations primarily sample the topographic portion
- connected the IVERT observations back to the source data they are evaluating

The next question is:

> **Can we reuse the same workflow somewhere else with different local data?**

Next we will move to **Sarasota, Florida**.
