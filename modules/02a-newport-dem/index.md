---
title: "2 - Build the Newport DEM"
---

# Build the Newport DEM

Now we will build our main workshop DEM for **Newport, Oregon**.

The workflow is:

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
```

We will start the build in this module and let it run while we move into **Module 2b — Inside the Workflow**.

---

# 1. Newport study area

Our study area includes **Yaquina Bay**, the entrance channel, nearby coastal waters, and adjacent topography.

```text
West:  -124.10
East:  -124.00
South:   44.59
North:   44.64
```

We will build an approximately **1/9-arc-second** DEM referenced to:

```text
Horizontal: NAD83
Vertical:   NAVD88
```

represented in the workflow as:

```text
epsg:4269+5703
```

<!-- IMAGE PLACEHOLDER
High-value figure:
Map or screenshot showing the Newport workshop AOI around Yaquina Bay.

Ideally show:
- coastline
- Yaquina Bay
- workshop bounding box
- enough regional context to orient participants

Suggested caption:
"Newport workshop study area around Yaquina Bay, Oregon."
-->

:::{tip}
You do not need to memorize the coordinates or EPSG codes.

The important idea is that every DEM recipe begins with a **study area**, **resolution**, and **target reference system**.
:::

---

# 2. Start with a national coastal data recipe

We are not starting from an empty list of datasets.

Our reusable starting recipe is:

```text
coupling-bathy-topo
```

This bundle brings together strong **nationally available coastal elevation data sources** that can provide a useful starting point almost anywhere along the U.S. coast.

Let's ask Fetchez what is in the bundle.

Run:

```bash
fetchez modules bundles info coupling-bathy-topo
```

:::{important}
## Success check

You should see a summary of the datasets and options included in the `coupling-bathy-topo` bundle.

Do not worry about every parameter yet. Focus on the **types of elevation data** represented.
:::

The bundle may include sources such as:

| Source | What it contributes |
|---|---|
| **The National Map (TNM)** | National topographic elevation data |
| **NOAA hydrographic surveys** | Measured bathymetry, including BAG and point-based survey data |
| **USACE eHydro** | Hydrographic surveys from navigation channels and waterways |
| **NOAA nautical charts** | Supplemental chart-derived bathymetric information |
| **Crowdsourced bathymetry (CSB)** | Additional depth observations where available |

:::{note}
The exact bundle contents and options may evolve as the Continuous DEMs tools are updated, and not every source has useful coverage in every study area.
:::

<!-- IMAGE PLACEHOLDER
Suggested visual:
A simple diagram showing several national data sources feeding into
`coupling-bathy-topo`.

Example concept:

TNM ─────────────┐
NOAA hydro ──────┤
eHydro ──────────┤
Charts ──────────┼──> coupling-bathy-topo
CSB ─────────────┘

Suggested caption:
"The coupling-bathy-topo bundle provides a reusable national-scale starting recipe."
-->

These sources give us a strong baseline:

```text
nationally available elevation data
                ↓
      coupling-bathy-topo
                ↓
       starting DEM recipe
```

But national coverage is only the beginning.

Coastal regions often have **local or regional surveys** that are newer, denser, or better suited to a particular study area.

So our next question is:

> **Is there a useful local dataset that would improve the Newport recipe?**

---

# 3. Find local topobathymetric lidar

NOAA's **Digital Coast Data Access Viewer (DAV)** lets us search geographically for coastal lidar.

Open DAV already focused on our Newport study area:

[Open NOAA Digital Coast DAV for Newport](https://coast.noaa.gov/dataviewer/#/lidar/search/-13815132.111459369,5557767.363023302,-13802740.194591334,5557767.363023302,-13802740.194591334,5565765.68390708,-13815132.111459369,5565765.68390708,-13815132.111459369,5557767.363023302)

<!-- IMAGE PLACEHOLDER
Suggested figure:
Screenshot of DAV zoomed to the Newport workshop area with the relevant
lidar footprints/results visible.

Suggested caption:
"NOAA Digital Coast DAV lets us search for lidar that overlaps the Newport study area."
-->

:::{important}
## Your turn: choose the local dataset

Look at the lidar datasets intersecting the workshop area.

Find a dataset that:

1. overlaps our Newport DEM region
2. contains **topobathymetric lidar**
3. would be useful for a combined land-and-water DEM

Select the dataset to open its details.
:::

---

# 4. Find the DAV dataset ID

Globato/Fetchez needs the numeric DAV dataset ID.

The DAV details panel does not prominently display that number, so we can get it from the **Bulk Download** link.

In the dataset details:

1. Scroll down to **Bulk Download**.
2. Click **Link to All Dataset Files**.
3. A new page will open showing the files for that dataset.
4. Look at the URL in your browser.

<!-- IMAGE PLACEHOLDER
Very high-value figure:
Screenshot of the DAV dataset details panel with:
- "Bulk Download" highlighted
- "Link to All Dataset Files" highlighted or arrowed

This can be the screenshot supplied by the instructor/user.

Suggested caption:
"Use Bulk Download → Link to All Dataset Files to expose the numeric DAV dataset ID."
-->

Near the end of the URL, you will see the dataset number:

```text
.../9693/index.html
     ↑
 DAV dataset ID
```

DAV calls this the **dataset ID**.

Globato/Fetchez uses the same number with the `dav` source as:

```text
survey_id=9693
```

So try completing:

```text
dav:survey_id=____,weight=100
```

:::{dropdown} Reveal the Newport dataset

The dataset is:

**2020 USACE NCMP Topobathy Lidar: Oregon**

Its DAV dataset ID is:

```text
9693
```

So the source specification is:

```text
dav:survey_id=9693,weight=100
```

:::

:::{tip}
## You do not need to download the files from DAV

We are using DAV to **discover and inspect** the local dataset.

Once we know the dataset ID, Globato/Fetchez can access it through the `dav` source.

You can close the Bulk Download page after identifying the number.
:::

:::{dropdown} Short on time?

If you are still looking for the survey when the group is ready to continue, use:

```text
dav:survey_id=9693,weight=100
```

and move on with the workshop.

You can return to DAV later.
:::

---

# 5. Put the recipe together

We now have:

```text
national coastal recipe
coupling-bathy-topo
        +
local Newport topobathymetric lidar
dav:survey_id=9693,weight=100
```

<!-- IMAGE PLACEHOLDER
Suggested visual:
Simple "national + local" recipe diagram.

Example:

coupling-bathy-topo
        +
DAV 9693
        ↓
Newport DEM recipe

Suggested caption:
"A reusable national recipe can be improved with locally appropriate data."
-->

This is one of the central ideas of the workshop:

> **Start with strong nationally available data, then add local scientific knowledge when it improves the DEM.**

---

# 6. Start the Newport build

Run:

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
## Start this command now

Leave the build running.

The terminal should begin printing workflow and data-access messages as Globato starts processing the recipe.

Once the build has started, **do not wait for it to finish here**. Continue with the rest of this page and then move to Module 2b.
:::

<!-- OPTIONAL CODE/OUTPUT PLACEHOLDER
If useful, add a small screenshot or short copied terminal excerpt showing
what a healthy build looks like immediately after launch.

Suggested caption:
"Example of Globato beginning the Newport workflow."

Avoid a long terminal dump. 5–10 representative lines is enough.
-->

---

# 7. Can you read the build command?

While the DEM is running, look back at the command.

Can you identify:

1. **Where** are we building the DEM?
2. **Which local dataset** did we add?
3. **What output resolution** are we requesting?
4. **What horizontal + vertical reference system** are we using?
5. **Which part represents the reusable national recipe?**

:::{dropdown} Check your answers

| Question | Answer |
|---|---|
| Study area | `-R -124.1/-124/44.59/44.64` |
| Local dataset | `dav:survey_id=9693,weight=100` |
| Resolution | `-E 0.1111111s` |
| Reference system | `-P epsg:4269+5703` = NAD83 + NAVD88 |
| National recipe | `coupling-bathy-topo` |

Other important pieces are:

| Part | Purpose |
|---|---|
| `-X 6:5` | #cells to extend DEM beyound study area:input data buffer percentage |
| `-O newport` | Output name |
| `-D newport_cudem` | Processing/output directory |
| `--shared-cache coupling-shared-dir` | Shared workshop source-data cache |

:::

---

# 8. What would change somewhere else?

Now that the Newport build is already running, consider another coastal region.

Which parts would probably stay the same?

Which parts would change?

:::{dropdown} Reusable or region-specific?

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

The pattern is:

```text
reusable coastal workflow
        +
local data choices
        +
new study area
        ↓
new coastal DEM
```

We will test this directly later with **Sarasota, Florida**.
:::

---

# 9. Continue while Newport builds

Keep the Newport terminal running.

Next, go to:

> **Module 2b — Inside the Workflow**

There we will connect the messages appearing in the terminal to the major processing stages:

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

:::{important}
## Ready for Module 2b?

You are ready to continue when:

- [ ] you found or were given DAV dataset ID `9693`
- [ ] the Newport `globato build` command is running
- [ ] the terminal is printing workflow messages

You do **not** need to wait for the DEM to finish.
:::
