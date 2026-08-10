---
title: "4 - Same Recipe, New Region"
---

# Same Recipe, New Region

We built and evaluated a coastal DEM for **Newport, Oregon**.

For the final exercise, we will test whether the same workflow can be reused in a very different coastal setting:

> **Sarasota, Florida**

The goal is not to build a completely new workflow.

The goal is to decide:

```text
what stays the same?
        +
what changes locally?
        ↓
new coastal DEM
```

:::{important}
## If we only have a few minutes left

Focus on three things:

1. Identify what changes from Newport to Sarasota.
2. Find the Sarasota DAV dataset ID.
3. Launch the Sarasota build.

The Sarasota DEM does **not** need to finish during the workshop.
:::

---

# 1. Move to Sarasota

Our Sarasota study area is:

```text
West:  -82.59
East:  -82.53
South:  27.28
North:  27.34
```

<!-- IMAGE PLACEHOLDER
High-value figure:

Map of the Sarasota workshop AOI.

Ideally show:
- Sarasota coastline
- workshop bounding box
- enough regional context to orient participants

Suggested caption:
"Sarasota workshop study area on the west coast of Florida."
-->

Compare this with Newport:

```text
Newport, Oregon
        ↓
rocky Pacific coast + Yaquina Bay

Sarasota, Florida
        ↓
low-relief Gulf coast + shallow nearshore waters
```

The coastal setting changes substantially.

The workflow should not have to start over.

---

# 2. What should stay the same?

Before looking at the answer, think back to the Newport command.

Which parts should probably remain reusable?

Which parts should change for Sarasota?

:::{important}
## Your turn

Classify these as either:

**mostly reusable**

or

**region-specific**

```text
coupling-bathy-topo
-R bounds
-P epsg:4269+5703
-X 6:5
-E 0.1111111s
local DAV dataset
output name
output directory
shared-cache location
```
:::

:::{dropdown} Check your answer

**Mostly reusable**

```text
coupling-bathy-topo
-P epsg:4269+5703
-X 6:5
-E 0.1111111s
```

**Region-specific**

```text
-R bounds
local DAV dataset
output name
output directory
shared-cache location
```

That separation is central to the framework:

```text
reusable coastal recipe
        +
local choices
        ↓
region-specific DEM
```

:::

<!-- IMAGE PLACEHOLDER
Suggested visual:

A simple Newport → Sarasota transfer diagram.

Example:

Newport
[AOI + DAV 9693]
        \
         > coupling-bathy-topo + common settings
        /
Sarasota
[AOI + DAV ????]

Suggested caption:
"Most of the workflow is reusable; the study area and locally appropriate data change."
-->

---

# 3. Find useful local Sarasota data

Just as we did for Newport, we will start from:

```text
coupling-bathy-topo
```

and then look for a locally useful **topobathymetric lidar dataset**.

Open NOAA Digital Coast DAV already focused on the Sarasota workshop area:

[Open NOAA Digital Coast DAV for Sarasota](https://coast.noaa.gov/dataviewer/#/lidar/search/-9198602.505474905,3155893.0057734232,-9186314.531330857,3155893.0057734232,-9186314.531330857,3166415.89275869,-9198602.505474905,3166415.89275869,-9198602.505474905,3155893.0057734232)

<!-- IMAGE PLACEHOLDER
Suggested figure:
DAV screenshot centered on the Sarasota AOI with lidar results/footprints visible.

Suggested caption:
"NOAA Digital Coast DAV lets us search for local lidar that overlaps the Sarasota study area."
-->

:::{important}
## Your turn: choose the local dataset

Look at the lidar datasets intersecting this small area.

Find the dataset that:

1. overlaps the Sarasota study area
2. contains **topobathymetric lidar**
3. would be useful for a combined land-and-water DEM

Select it to open the dataset details.
:::

---

# 4. Find the DAV dataset ID

Use the same method we used in Newport.

In the dataset details:

1. Scroll to **Bulk Download**.
2. Click **Link to All Dataset Files**.
3. A new page will open showing the dataset files.
4. Look at the URL in your browser.

<!-- IMAGE PLACEHOLDER
Very high-value figure:

Screenshot of:
Bulk Download
    ↓
Link to All Dataset Files

Reuse the same visual style as the Newport instructions if possible.

Suggested caption:
"Use Bulk Download → Link to All Dataset Files to find the DAV dataset ID."
-->

Near the end of the URL, you will see:

```text
.../XXXXX/index.html
      ↑
  DAV dataset ID
```

DAV calls this the **dataset ID**.

Globato/Fetchez uses that same number as:

```text
survey_id=XXXXX
```

Try completing:

```text
dav:survey_id=____,weight=100
```

:::{dropdown} Reveal the Sarasota dataset

The dataset is:

**2024 USACE FEMA Topobathy Lidar: Post Hurricane Milton, FL**

Its DAV dataset ID is:

```text
10196
```

So the Globato source specification is:

```text
dav:survey_id=10196,weight=100
```

Compare that with Newport:

```text
Newport:   dav:survey_id=9693,weight=100
Sarasota:  dav:survey_id=10196,weight=100
```

The source syntax stays the same.

Only the locally appropriate dataset changes.

:::

:::{tip}
## You do not need to download the files from DAV

DAV is helping us **discover and inspect** the local dataset.

Once we know the ID, Globato/Fetchez can access it through the `dav` source.
:::

:::{dropdown} Short on time?

If the group is ready to move on, use:

```text
dav:survey_id=10196,weight=100
```

and continue.

The point of the exercise is understanding how the local dataset enters the reusable recipe.
:::

---

# 5. Edit the Newport recipe

Before revealing the complete Sarasota command, look at the Newport command and decide what you would change.

Newport used:

```bash
globato build \
  -R -124.1/-124/44.59/44.64 \
  -X 6:5 \
  -P epsg:4269+5703 \
  -E 0.1111111s \
  -O newport \
  -D newport_cudem \
  --shared-cache coupling-shared-dir \
  coupling-bathy-topo \
  dav:survey_id=9693,weight=100
```

:::{important}
## Your turn: change only what is necessary

For Sarasota, update:

- the region
- the local DAV dataset
- the output name
- the output directory
- the cache location

Leave the reusable parts alone.
:::

:::{dropdown} Reveal the Sarasota command

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

:::

---

# 6. Launch the Sarasota build

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
## Success check

You do **not** need to wait for the DEM to finish.

You have completed the main exercise once:

- the command starts successfully
- Globato begins discovering or reusing data
- the Sarasota DAV source appears in the workflow

That is enough to demonstrate that the recipe transferred to the new region.
:::

<!-- TERMINAL SCREENSHOT PLACEHOLDER
Optional:
Short screenshot showing a healthy Sarasota build beginning.

Suggested caption:
"The Sarasota build starts with the same workflow structure used for Newport."
-->

---

# 7. Compare Newport and Sarasota

While Sarasota starts, compare the two builds.

| Setting | Newport | Sarasota |
|---|---|---|
| Region | `-124.1/-124/44.59/44.64` | `-82.59/-82.53/27.28/27.34` |
| National bundle | `coupling-bathy-topo` | `coupling-bathy-topo` |
| Local DAV dataset | `9693` | `10196` |
| Resolution | ~1/9 arc-second | ~1/9 arc-second |
| Reference system | NAD83 + NAVD88 | NAD83 + NAVD88 |
| DEM buffer settings | `-X 6:5` | `-X 6:5` |

:::{important}
## One-sentence challenge

Complete:

> **To move the workflow from Newport to Sarasota, we kept __________ and changed __________.**
:::

:::{dropdown} One possible answer

> **We kept the reusable coastal recipe and processing settings, and changed the study area, locally appropriate DAV dataset, and output/cache locations.**

:::

---

# 8. Visual payoff

Even if your Sarasota build is still running, look at the instructor-provided Sarasota result.

<!-- IMAGE PLACEHOLDER
Highest-value final-workshop visual:

Side-by-side:
1. Newport hillshade
2. Sarasota hillshade

Use comparable framing if possible.

Suggested caption:
"The same coastal DEM recipe applied to two very different coastal settings."

This image is especially valuable because it gives participants a visual payoff
even if their Sarasota build does not finish during the workshop.
-->

Ask:

- How does the coastal morphology differ?
- Where does the surface look especially detailed?
- Where does interpolation appear to play a larger role?
- What local dataset made sense to add in each region?

Do not perform a full Sarasota evaluation here.

The purpose is to see that the **workflow transfers**.

---

:::{dropdown} If your Sarasota DEM finishes

Look inside:

```text
sarasota_dem/
```

If the final products are available, open the DEM or hillshade and compare them briefly with Newport.

Focus on the large-scale difference in coastal setting rather than performing another full analysis.

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
inspect sources and processing
        ↓
evaluate with appropriate independent observations
        ↓
reuse the workflow somewhere else
```

<!-- IMAGE PLACEHOLDER
Optional final summary graphic:
A polished version of the workflow above, with Newport and Sarasota as
two example branches from the same reusable recipe.

Suggested caption:
"A reusable workflow can combine national-scale data with local scientific knowledge in different coastal regions."
-->

---

# Final takeaway

The most important result of the workshop is not a single Newport or Sarasota DEM.

It is the framework:

> **Start with a reusable national-scale recipe, add locally appropriate data, and keep the workflow transparent enough to inspect and evaluate.**

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

