---
title: "2b - Inside the Workflow"
---

# Inside the Workflow

The Newport DEM should now be running.

Keep the terminal visible as we walk through the major stages of the workflow.

You do **not** need to interpret every message. Instead, try to recognize this overall sequence:

```text
discover data
      ↓
prepare each source
      ↓
standardize reference systems
      ↓
prioritize and stack measurements
      ↓
interpolate across multiple scales
      ↓
write the final DEM
```

<!-- IMAGE PLACEHOLDER
Highest-value figure for this module:

A single workflow diagram showing:

National + local data sources
        ↓
Fetchez
        ↓
source-specific processing
        ↓
Transformez / reference-system standardization
        ↓
weighted stacking
        ↓
Globato multiresolution interpolation
        ↓
final DEM + hillshade

Suggested caption:
"Major stages in the Newport coastal DEM workflow."

If possible, use the same terminology that appears in the terminal output.
-->

:::{tip}
## Follow the stages, not every log line

As the build runs, periodically ask:

> **Which stage of the workflow are we seeing right now?**

That is more useful than trying to understand every terminal message.
:::

---

# The tools working together

Three Continuous-DEMs tools support the build:

| Tool | Main role |
|---|---|
| **Fetchez** | Orchestrates the workflow, manages the recipe, and discovers and streams source data |
| **Transformez** | Supports horizontal and vertical coordinate transformations |
| **Globato** | Provides the elevation-specific tools for filtering, gridding, and building the DEM |

We launch the workflow with **Globato**:

```text
globato build ...
```

From the user's perspective, Globato is the main entry point for building a DEM.

Under the hood, **Fetchez coordinates the data pipeline**, while Globato provides the elevation-specific processing needed to turn those source data into a DEM. **Transformez** supports the coordinate and vertical-reference transformations required along the way.

Our Newport recipe combines:

```text
coupling-bathy-topo
        +
dav:survey_id=9693,weight=100
```

The bundle provides our strong nationally available starting point.

The DAV source adds the locally useful Newport topobathymetric lidar.

Together, Fetchez, Transformez, and Globato turn that recipe into the final coastal DEM.


---

# 1. Discover the data

The workflow first determines which configured sources actually overlap the Newport study area.

Different providers may use different:

- catalogs
- APIs
- file structures
- formats
- download methods

Fetchez gives the larger workflow a common way to access those sources.

## Explore Fetchez

The Newport build is already using Fetchez behind the scenes.

Open a **second terminal** so the Globato build can continue running, then list the available data modules:

```bash
fetchez modules list
```

You can inspect the national coastal bundle used for Newport:

```bash
fetchez modules bundles info coupling-bathy-topo
```

If a particular source interests you, inspect it with:

```bash
fetchez modules info MODULE_NAME
```

For example, learn more about the USACE ehdyro module:
```text
fetchez modules info ehydro
```

Recall that we also added the Newport Digital Coast lidar directly:

```text
dav:survey_id=9693,weight=100
```

Learn more about the Digital Coast module dav:
```text
fetchez modules info dav
```

So the Newport source recipe combines:

```text
national coastal data
        +
local Newport topobathymetric lidar
```

:::{tip}
You do not need to explore every Fetchez module.

The goal is simply to see that the individual data providers are accessible components that Globato can combine into a larger DEM recipe.
:::

As the terminal runs, watch for data being:

- discovered
- opened
- downloaded
- reused from cache

<!-- TERMINAL SCREENSHOT PLACEHOLDER
Suggested figure:
A short screenshot or cropped terminal excerpt showing one or two
representative source-discovery messages.

Keep it short enough that participants can visually match the screenshot
to their own terminal.

Suggested caption:
"Example of source discovery during the Newport build."
-->

:::{important}
## Can you spot a source?

Look at your running terminal.

Can you recognize the name of **one dataset or provider** from the national bundle or the local DAV lidar?
:::

---

# Shared workshop data

Our command includes:

```text
--shared-cache coupling-shared-dir
```

The logic is simple:

```text
source already cached?
        │
        ├── yes → reuse it
        │
        └── no  → access it
```

For the workshop, this avoids unnecessary duplicate downloads.

Your own Newport processing and output files are still written to:

```text
newport_cudem/
```

<!-- OPTIONAL VISUAL PLACEHOLDER
A tiny schematic may help:

shared source cache  →  reused by participants
participant workspace → Newport processing + outputs

Suggested caption:
"Shared source data reduce duplicate downloads while each participant keeps their own DEM outputs."
-->

:::{tip}
If you see messages about **reusing cached data**, that is expected.

The cache is part of the workshop setup, not a shortcut around the workflow.
:::

---

# 2. Prepare each source appropriately

Coastal elevation datasets are not all equivalent.

Depending on the source, the workflow may need to:

- clip data to the study area
- unpack or convert files
- filter inappropriate elevations
- apply land/water constraints
- transform coordinates or vertical datums

The important idea is:

> **Use one reproducible workflow while still treating each source according to what that source requires.**

This matters because topographic lidar, hydrographic surveys, chart data, and other coastal observations were collected differently and should not automatically be processed in exactly the same way.

---

:::{dropdown} Why does the coastline matter?

A coastal DEM crosses a difficult boundary between dense terrestrial measurements and often sparser bathymetric observations.

We do not want dense land elevations influencing interpolation far offshore.

A land/water boundary can also help distinguish useful terrain or seafloor elevations from inappropriate water-surface elevations.

Conceptually:

```text
land measurements      water measurements
        \                    /
         \                  /
          coastline constraint
                  ↓
          coastal DEM surface
```

The coastline is therefore part of the processing logic, not just a map feature.

<!-- IMAGE PLACEHOLDER
Optional but useful:
A simple cross-section or map showing dense land lidar, coastline,
and sparse bathymetric measurements.

Suggested caption:
"Coastline constraints help keep dense terrestrial measurements from influencing offshore interpolation inappropriately."
-->
:::

---

# 3. Standardize the reference system

Our target DEM is requested in:

```text
epsg:4269+5703
```

which represents:

```text
Horizontal: NAD83
Vertical:   NAVD88
```

Source datasets may arrive in different horizontal or vertical reference systems.

Before those measurements can be combined meaningfully, they need to be expressed in the requested common reference system.

Watch the terminal for transformation steps.

This is where **Transformez** and the broader transformation framework support the workflow.

## Explore Transformez

In your second terminal, list the available transformations:

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

You do not need to run the transformations manually here. Globato coordinates the transformations required by the recipe.

:::{tip}
This modular design is useful because the individual capabilities remain available on their own:

```text
Fetchez      → discover and access data
Transformez  → transform reference systems
Globato      → coordinate the DEM workflow
```
:::

<!-- TERMINAL SCREENSHOT PLACEHOLDER
Suggested figure:
A short cropped terminal excerpt showing a representative transformation
or reprojection message.

Suggested caption:
"Source data are transformed into the requested horizontal and vertical reference system before stacking."
-->

:::{important}
## Can you spot a transformation?

Look at the terminal.

Can you find a message related to:

- reprojection
- coordinate transformation
- vertical transformation
- EPSG / datum information

You only need to identify the stage—not interpret every parameter.
:::

:::{dropdown} Why is the vertical datum important?

The same physical location can have different numerical elevation values when referenced to different vertical surfaces.

A vertical transformation changes the **reference used to describe the elevation**. It does not change the physical terrain or seafloor.

That standardization is essential before elevations from different sources can be stacked together.
:::

---

# 4. Prioritize and stack the measurements

Once the source data are prepared and standardized, the measurements are brought into a common raster framework.

Where datasets overlap, their relative priorities help determine which information should dominate.

Conceptually:

```text
higher-priority measurements
          ↓
lower-priority measurements
          ↓
supplemental observations
          ↓
remaining gaps
```

For Newport, we explicitly added:

```text
dav:survey_id=9693,weight=100
```

so the locally selected topobathymetric lidar receives strong priority where it overlaps lower-priority sources.

<!-- IMAGE PLACEHOLDER
High-value figure:
A simple stacking diagram with overlapping sources and the higher-priority
source shown on top.

Suggested caption:
"Source priorities determine which measurements dominate where datasets overlap."
-->

:::{note}
A source weight is a **recipe priority**, not a formal measurement uncertainty.
:::

---

:::{dropdown} If you see Z, W, and U in the terminal

You may see output containing:

```text
Z: [...]
W: [...]
U: [...]
```

A useful shorthand is:

| Value | Meaning |
|---|---|
| `Z` | Elevation |
| `W` | Source priority / weight |
| `U` | Uncertainty information when available |

The reported `Z` range can provide a quick reality check on the elevations being processed.

`W` reflects the recipe priorities used when sources overlap.

Not every input provides explicit uncertainty information, so `U` may be absent or zero for some sources.
:::

---

# 5. Interpolate across multiple scales

Measurement density can change dramatically across a coastal region.

Dense lidar may support very fine spatial detail:

```text
• • • • • • • •
• • • • • • • •
• • • • • • • •
```

Bathymetric observations may be much farther apart:

```text
•                    •


          •


                         •
```

The key idea is:

> **Use fine spatial information where the observations support it, and progressively broader information where measurements are sparse.**

Conceptually:

```text
dense measurements
        ↓
fine-scale information

sparser measurements
        ↓
broader interpolation
```

<!-- IMAGE PLACEHOLDER
Highest-value conceptual figure after the main workflow diagram:

Show:
dense lidar → fine interpolation scale
sparse bathymetry → broader interpolation scale
then both contributing to one continuous DEM.

Suggested caption:
"Multiresolution processing allows the interpolation scale to broaden as measurement density decreases."
-->

:::{important}
## Can you spot the multiresolution stage?

Look for messages showing processing at progressively different scales or resolutions.

You do not need to follow the interpolation mathematics.

The goal is simply to recognize when the workflow has moved from **preparing measurements** to **building the continuous surface**.
:::

---

# 6. Write the final outputs

When processing is complete, Globato writes the final DEM and supporting products.

The two primary workshop products are:

```text
newport_cudem/newport_final.tif
newport_cudem/newport_hs.tif
```

where:

```text
newport_final.tif   finished Newport coastal DEM
newport_hs.tif      hillshade for visual inspection
```

<!-- IMAGE PLACEHOLDER
Very useful payoff image:
Side-by-side screenshot of:
- newport_final.tif
- newport_hs.tif

Suggested caption:
"Primary Newport outputs: the finished coastal DEM and hillshade."
-->

:::{dropdown} If your build finishes early

You can confirm the two main products with:

```bash
ls -lh newport_cudem/newport_final.tif newport_cudem/newport_hs.tif
```

Do not spend much time interpreting them yet.

We will explore the surface and supporting outputs in **Module 3**.
:::

---

# What stage are we in now?

Look back at the terminal one more time.

Which of these best describes what you see?

- discovering or accessing data
- reusing cached data
- preparing or filtering a source
- transforming coordinates or elevations
- stacking measurements
- interpolating across multiple scales
- writing final outputs

:::{tip}
If your terminal is showing something different from the instructor's, that does not necessarily mean something is wrong.

Different datasets and cached files can cause participants to move through stages at slightly different times.
:::

---

# If your build is still running

That is okay.

The goal of this module is to understand the **shape of the workflow**, not to wait on the same terminal line as everyone else.

<!-- TODO / RESCUE PATH PLACEHOLDER
Add the exact instructor-provided fallback location once known.

Suggested wording:

"If your Newport build is still running or encountered an error, use the
instructor-provided Newport outputs in:

[EXACT SHARED OUTPUT PATH]

Copy/open:
- newport_final.tif
- newport_hs.tif
- any provenance/source products needed for Module 3

Then continue with the group."
-->

:::{important}
## Ready for Module 3?

Continue when either:

- your Newport build has finished, **or**
- the instructor has directed you to the provided Newport outputs.

Next:

> **Module 3 — Explore and Evaluate the DEM**
:::
