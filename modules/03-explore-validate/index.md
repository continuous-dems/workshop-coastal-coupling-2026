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
inspect the spatial metadata
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

The hillshade shows us the shape of the finished surface.

The **spatial metadata** helps us understand where the elevation data came from.

Globato writes a spatial-metadata GeoPackage in the Newport output directory with a name similar to:

```text
newport_n44x64_w124x10_sm.gpkg
```

The exact filename is based on the DEM tile or region, so yours may differ slightly.

First, find it:

```bash
ls -lh newport_cudem/*_sm.gpkg
```

:::{important}
## Success check

You should see a spatial-metadata GeoPackage ending in:

```text
_sm.gpkg
```

This is one of the most useful supporting products from the build.
:::

---

## View the spatial metadata with the hillshade

The most useful way to inspect this product is to view the spatial metadata **on top of the Newport hillshade**.

Use:

```text
newport_cudem/newport_hs.tif
```

as the background, and overlay:

```text
newport_cudem/*_sm.gpkg
```

<!-- CODE PLACEHOLDER
Add the exact workshop visualization command or notebook code here once finalized.

The code should:
1. display `newport_hs.tif`
2. overlay the features from the `*_sm.gpkg`
3. symbolize or label the spatial metadata by source dataset
4. make it easy to distinguish directly supported areas from areas where
   interpolation is more important

Example placeholder:

```python
# TODO: exact visualization code
hillshade = "newport_cudem/newport_hs.tif"
spatial_metadata = "newport_cudem/newport_n44x64_w124x10_sm.gpkg"

# display hillshade
# overlay spatial metadata
# symbolize by source
```
-->

<!-- IMAGE PLACEHOLDER
HIGHEST-VALUE FIGURE FOR THIS SECTION:

A Newport hillshade with the spatial-metadata GeoPackage overlaid.

Ideally:
- use the Newport hillshade as a grayscale background
- show the spatial metadata with transparent fills or outlines
- distinguish source datasets clearly
- include a concise legend
- preserve enough hillshade visibility to see Yaquina Bay, the entrance
  channel, coastline, and surrounding topography

Suggested caption:
"Newport hillshade with Globato spatial metadata showing where source elevation datasets contribute to the DEM."
-->

The spatial metadata lets us connect the finished surface back to the data used to build it.

Instead of asking only:

> **What does the DEM look like here?**

we can also ask:

> **What source data support this part of the DEM?**

---

## Your turn: connect the surface to the source data

Return to the location you picked in the previous section.

Using the hillshade and spatial metadata together, ask:

1. Which source dataset covers this location?
2. Is there more than one source nearby?
3. Does the hillshade look especially detailed where dense measurements are available?
4. Where do the spatial metadata become sparse or absent?
5. Where might interpolation therefore play a larger role?

:::{tip}
## Spatial metadata and interpolation

The spatial metadata identifies where source elevation data contribute to the DEM.

Areas without direct source-data coverage are especially important to recognize because the final surface there depends more heavily on interpolation between available measurements.

The hillshade shows the **surface**.

The spatial metadata shows the **measurement support behind that surface**.
:::

---

## Inspect the metadata attributes

The GeoPackage also contains attributes describing the source-data features.

<!-- TODO / CODE PLACEHOLDER
Once the final spatial-metadata schema is confirmed, add one simple command
or short Python snippet that prints only the useful columns.

For example, show:
- source/provider
- dataset or survey identifier
- weight/priority if present
- any field that distinguishes source-data support from interpolation

Do not expose a long table of implementation-specific fields.
-->

:::{important}
## One-location challenge

Pick one location in the Newport DEM and be able to describe it in one sentence:

> **This part of the DEM is supported by __________, and the surrounding surface appears __________.**

The purpose is not to catalog every source.

It is to learn how to use the spatial metadata to interpret the finished DEM.
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

# 5. Evaluate the DEM with IVERT

So far, we have looked at:

- the finished Newport DEM
- the hillshade
- the spatial metadata showing where source elevation data contribute

Those products help us understand **how the DEM was built**.

Next, we want to ask a different question:

> **How well does the finished DEM agree with independent elevation observations?**

For that, we will use **IVERT**.

IVERT provides a repeatable way to compare a DEM with independent observations and generate useful validation products.

In this workshop, we will use available **ICESat-2 observations** as an independent check on the Newport DEM.

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

<!-- IMAGE PLACEHOLDER
Suggested visual:

A simple IVERT concept diagram showing:

Newport DEM
     +
ICESat-2 observations
     ↓
IVERT
     ↓
comparison statistics + spatial outputs

Suggested caption:
"IVERT compares a DEM with independent observations to support quantitative and spatial evaluation."
-->

:::{important}
## Why use independent observations?

The measurements used to build a DEM tell us what data support the surface.

Independent observations give us a separate reference for evaluating the finished result.

Together, they answer two different questions:

```text
Spatial metadata:
Where did the DEM elevations come from?

IVERT:
How does the finished DEM compare with independent observations?
```
:::

:::{dropdown} Why not just inspect the hillshade?

Visual inspection is extremely useful for finding obvious artifacts, discontinuities, or unrealistic terrain.

But a DEM can look reasonable and still contain vertical bias or other elevation differences.

IVERT complements visual inspection by adding an independent, quantitative comparison.

The two approaches work best together:

```text
visual inspection
        +
independent validation
        ↓
stronger DEM evaluation
```

:::

Now we will get the available independent observations for Newport and run the comparison.

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

If possible, pair it with the spatial metadata map using the same extent.

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

# 10. Connect validation back to spatial metadata

Much of the terrestrial portion of the Newport DEM is supported by the USGS national elevation data included in our standard coastal recipe.

Return to the spatial metadata.

Ask:

> **Which source data support the locations sampled by the IVERT observations?**

This connects:

```text
independent observations
        ↓
sampled part of DEM
        ↓
source data identified in the spatial metadata
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
spatial metadata
```

You do not need to do this for every point.

One example is enough to demonstrate the reasoning process.
:::

---

# 11. What would we need to evaluate the bathymetry?

If we wanted to independently evaluate the bathymetric portion of the Newport DEM, we would need appropriate independent observations in the water.

Examples could include:

- ICESat-2 ATL24 data product
- independent hydrographic soundings
- withheld sonar observations
- independent bathymetric lidar

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
- used the spatial metadata to connect the finished surface back to its source data
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
