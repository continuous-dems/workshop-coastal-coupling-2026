---
title: "8 - Vertical Transformation with transformez"
---

`transformez` supports vertical datum and reference-frame transformation workflows. In coastal DEM development, vertical consistency is critical because topographic and bathymetric datasets often come from different sources and may use different vertical references.

A coastal DEM may combine data referenced to:

- An ellipsoid
- An EGM geoid model
- NAVD88
- Mean Lower Low Water
- Mean High Water
- Local tidal datums
- Chart datums

`fetchez` can prepare source data through hooks such as cropping, filtering, datatype assignment, and horizontal reprojection. `transformez` focuses on the vertical component: it generates vertical shift grids or applies vertical datum transformations to existing rasters, moving source elevations and depths into a common vertical reference before they are used in a DEM workflow.

The main concept is that datasets cannot be compiled reliably into one coastal DEM until their elevation and depth values are referenced consistently. During this workshop, the goal is to understand where `transformez` fits into the CUDEM workflow rather than to cover every vertical datum case in detail.

---

## Basic Help Command

Run:

```bash
transformez --help
```

The main command groups are:

| Command | Purpose |
|---|---|
| `grid` | Generate a standalone vertical shift grid |
| `raster` | Apply a vertical datum shift to an existing DEM |
| `list` | List supported vertical datums and EPSG options |
| `htdp` | Manage and run NGS HTDP |
| `vdatum` | Manage and run NOAA VDatum |

---

## List Supported Datums

Use `list` to see supported vertical datums and EPSG options:

```bash
transformez list
```

This is useful when you need to confirm the correct input or output datum name before running a transformation.

---

## Generate a Vertical Shift Grid

Use `transformez grid` when you want to create a standalone vertical shift grid for a region.

Inspect the available options:

```bash
transformez grid --help
```

General command pattern:

```bash
transformez grid -R WEST/EAST/SOUTH/NORTH -E RESOLUTION -I INPUT_DATUM -O OUTPUT_DATUM -o shift_grid.tif
```

Example:

```bash
transformez grid -R 178.25/178.65/-18.30/-17.95 -E 3s -I mllw -O 3855 -o fiji_shift_grid.tif
```

### Command Breakdown

| Command option | Purpose |
|---|---|
| `transformez grid` | Create a vertical shift grid |
| `-R WEST/EAST/SOUTH/NORTH` | Set the target region |
| `-E 3s` | Set the output resolution |
| `-I INPUT_DATUM` | Set the source or input vertical datum |
| `-O OUTPUT_DATUM` | Set the target or output vertical datum |
| `-o shift_grid.tif` | Set the output shift-grid filename |

Preview the shift grid:

```bash
transformez grid -R 178.25/178.65/-18.30/-17.95 -E 3s -I mllw -O 3855 --preview
```

The shift grid represents the vertical correction required to move values from the input datum to the output datum across the selected region.

---

## Apply a Vertical Datum Transformation to a Raster

Use `transformez raster` to apply a vertical datum shift to an existing DEM.

General pattern:

```bash
transformez raster INPUT_FILE -I INPUT_DATUM -O OUTPUT_DATUM [OPTIONS]
```

Example:

```bash
transformez raster input_dem.tif -I 3855 -O mhw --decay-pixels 0 -o output_dem.tif
```

Command breakdown:

- `transformez raster` — applies a vertical datum transformation to an existing raster.
- `INPUT_FILE` — input DEM or raster file.
- `-I 3855` — source vertical datum.
- `-O mhw` — target vertical datum.
- `--decay-pixels 0` — disables the inland decay of tidal transformation values.
- `-o output_dem.tif` — specifies the output filename.

By default, tidal transformation values may decay toward zero over inland areas. Setting `--decay-pixels 0` preserves the transformation surface without applying the inland decay.

Additional options:

- `--in-units` — units of the input DEM: `auto`, `m`, `ft`, or `us-ft`.
- `--out-units` — desired units of the output DEM.
- `--use-stations` — use live tide-station values instead of global tidal models.
- `--save-shift` — save the aligned vertical-shift grid alongside the output DEM.

---


## How `transformez` Connects to the Fiji Recipe

In the Fiji workflow, spatial reference information is defined in the YAML recipe through settings and hooks such as:

- `region_srs`
- `set-srs`
- `stream_reproject`

These settings tell the workflow:

- The horizontal and vertical reference assigned to each source dataset
- The target horizontal reference system
- The target vertical datum of the final DEM
- Which datasets require transformation before they can be combined

Source-level preparation can be handled with CUDEM hooks. For example, `set-srs` assigns reference information to a dataset, while `stream_reproject` converts it into the target coordinate reference system.

`transformez` handles the vertical relationship between the source and target datums. It can generate vertical shift grids or apply those shifts directly to rasters before the datasets are combined during DEM compilation.

The general workflow is:

```text
Assign source reference information
→ Reproject source data horizontally
→ Generate or apply the vertical datum shift
→ Combine the harmonized datasets
→ Build the final DEM
```


---

## What to Look For

When using `transformez`, check:

- Did I define the correct input datum?
- Did I define the correct output datum?
- Does the selected region cover the source dataset?
- Is the output resolution appropriate?
- Did the shift grid or transformed raster write successfully?
- Are the sign and magnitude of the vertical shift reasonable?
- Does the selected vertical reference make sense for the intended DEM application?

---

## Main Takeaway

`transformez` makes vertical datum handling explicit and automated within CUDEM workflows.

This is critical because vertical-reference mistakes can introduce significant offsets when combining topography, bathymetry, tidal datums, chart datums, and other elevation sources into a composite DEM.

The basic workflow is:

```text
identify the source vertical datum
→ identify the target vertical datum
→ generate or apply the vertical shift
→ inspect the transformed output
→ use the harmonized data in the DEM workflow
```

## Documentation

[Transformez documentation](https://transformez.readthedocs.io/en/latest/index.html)
