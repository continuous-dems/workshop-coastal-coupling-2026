---
title: "9 - DEM Workflows with globato"
---

`globato` connects the CUDEM workflow. It combines `fetchez` for data discovery, access, and source-level preparation; `transformez` for vertical datum and reference-frame transformation; and CUDEM tools for source filtering, weighting, stacking, interpolation, and output generation.

The result is a reproducible, recipe-based process that transforms diverse source datasets into a commonly referenced, composite DEM. It turns DEM building into a workflow that can be exported, inspected, modified, shared, rerun, and version-controlled.

In Day 1, we generated the Fiji test DEM and reviewed the YAML recipe used to define the workflow. In Day 2, we will focus on two primary `globato` commands:

```text
globato cudem build    Generate a DEM workflow from command-line options
globato cudem run      Execute a saved YAML recipe
```

The main concept is:

```text
build = define a DEM workflow using command-line options
run   = execute a saved YAML recipe
```

`globato` can also draw on module bundles that collect multiple source modules and their default settings under a single name, covered in the **Module Bundles** section below.

---

## Basic Help Commands

Start with:

```bash
globato --help
```

Inspect the CUDEM workflow commands:

```bash
globato cudem --help
```

View the available build options:

```bash
globato cudem build --help
```

View the recipe-run options:

```bash
globato cudem run --help
```

The `build` command is useful when you want to define a workflow from a region, resolution, target reference frame, interpolation method, and source bundle.

The `run` command is useful when you already have a saved YAML recipe and want to execute or modify that workflow.

---

# Module Bundles

Before defining a DEM workflow, it is useful to understand module bundles.

A module bundle is a preconfigured collection of source modules and processing settings. Instead of manually listing many individual data sources, a bundle allows a predefined group of sources to be referenced by one name.

In this workshop, we use:

```text
global-bathy-topo
```

This bundle brings together global topographic and bathymetric sources into one reusable source collection.

The main idea is:

```text
Instead of manually listing many source modules, use one named bundle.
```

---

## Inspect Available Module Bundles

View the module-bundle commands:

```bash
globato modules bundles --help
```

List available bundles:

```bash
globato modules bundles list
```

Example bundles may include:

```text
crm_alaska
crm_standard
global-bathy-topo
us_coastal_stream
```

Each bundle is designed for a different DEM-building use case.

---

## Inspect the `global-bathy-topo` Bundle

Print the YAML definition of the `global-bathy-topo` bundle:

```bash
globato modules bundles dump global-bathy-topo
```

This displays the source modules, arguments, hooks, weights, and other settings included when the bundle is used in a DEM workflow.

The `global-bathy-topo` bundle may include source modules such as:

```text
copernicus
copernicus_marine
mbdb
sysu
margrav
csb
```

It may also include processing hooks such as:

```text
unzip
filename_filter
set_datatype
range_z
set-srs
stream_reproject
rq
```

The bundle provides a practical starting point, but it is not a black box. Its contents can be inspected, exported into a complete YAML recipe, and modified for a specific region or project.

---

# Export a Fiji DEM Recipe

The Fiji YAML recipe used in this workshop can be generated from a `globato cudem build` command using the `--export` option.

Run:

```bash
globato cudem build -R 178.25/178.65/-18.30/-17.95 -E 3s -O fiji_test -P epsg:4326+3855 -M ms_binary_cudem:algos=interp_gmt -X 0:5 global-bathy-topo --export
```

This command defines a Fiji coastal DEM workflow using the `global-bathy-topo` bundle and writes the generated workflow to a YAML recipe.

The `--export` option exports the recipe **instead of running the DEM build**.

## Command Breakdown

| Command option | Purpose |
|---|---|
| `globato cudem build` | Generate a DEM workflow from command-line options |
| `-R 178.25/178.65/-18.30/-17.95` | Set the Fiji test region |
| `-E 3s` | Set the output resolution to 3 arc-seconds |
| `-O fiji_test` | Set the output basename |
| `-P epsg:4326+3855` | Set the target horizontal and vertical reference |
| `-M ms_binary_cudem:algos=interp_gmt` | use GMT interpolation algorithm |
| `-X 0:5` | Extend the processing region using a 5 percent buffer |
| `global-bathy-topo` | Use the global bathymetry-topography source bundle |
| `--export` | Write the generated workflow to a YAML recipe instead of building the DEM |

Expected recipe output:

```text
fiji_test_recipe.yaml
```

At this stage, the workflow has been exported but the final DEM has not yet been generated.

---

## Why Export the Recipe?

The exported YAML file records the workflow decisions used to define the DEM.

This may include:

- Project name
- Region
- Target reference system
- Source modules
- Source weights
- Per-source hooks
- Global hooks
- Stacking settings
- Interpolation settings
- Output files
- Execution settings

This is what makes the workflow reproducible. The command defines the workflow, and the exported YAML preserves it in a form that can be inspected, modified, shared, version-controlled, and rerun.

---

# Add Local Data with `local_fs`

The `local_fs` module searches a local directory for files that match a specified extension and adds them to the CUDEM workflow.

Example for local GeoTIFF files:

```text
modules:
  - module: local_fs
    args:
      path: /path/to/local/data
      ext: .tif
      datatype: raster
      weight: 1.0
      uncertainty: 0.0
      use_cache: true
```

Example for local XYZ point data:

```text
modules:
  - module: local_fs
    args:
      path: /path/to/local/xyz
      ext: .xyz
      datatype: xyz
      weight: 1.0
      use_cache: false
```

Hooks can be added to describe and prepare the local dataset before it enters the larger workflow:

```text
modules:
  - module: local_fs
    args:
      path: /path/to/local/xyz
      ext: .xyz
      datatype: xyz
      weight: 1.0
      use_cache: false
    hooks:
      - name: set-srs
        args:
          srs: EPSG:4326+3855
      - name: spatial_crop
      - name: range_z
        args:
          max_z: -0.1
```

This allows local lidar, multibeam, chart soundings, XYZ files, rasters, or other project-specific datasets to be combined with the remote source modules already included in the exported recipe.

General pattern:

```text
export recipe
→ add local_fs module
→ define local path and file type
→ assign source reference information
→ add optional filtering hooks
→ run the modified recipe
```


---

# Run a Saved YAML Recipe

Once a recipe has been exported, use `globato cudem run` to execute it.

General pattern:

```bash
globato cudem run RECIPE.yaml
```

Fiji example:

```bash
globato cudem run fiji_test_recipe.yaml
```

This command executes the saved workflow and generates the DEM and associated outputs.

Expected final DEM:

```text
fiji_test_final.tif
```

The recipe can now be rerun without reconstructing the original command.

---

## Override Recipe Settings

Parts of the saved recipe may be overridden from the command line.

Override the region:

```bash
globato cudem run fiji_test_recipe.yaml -R WEST/EAST/SOUTH/NORTH
```

Override the resolution:

```bash
globato cudem run fiji_test_recipe.yaml -E 1s
```

Override the output name:

```bash
globato cudem run fiji_test_recipe.yaml -O fiji_test_v2
```

This makes a saved recipe reusable across different regions, resolutions, and output configurations.

---

# Inspect the Output

After the recipe finishes, inspect the DEM metadata:

```bash
gdalinfo fiji_test_final.tif
```

Useful information in the `gdalinfo` output includes:

- Driver
- Raster size
- Coordinate system
- Origin
- Pixel size
- Corner coordinates
- Band information
- NoData value
- Minimum and maximum values

## Create a Hillshade

Create a quick visual-inspection product using `globato perspecto`:

```bash
globato perspecto hillshade fiji_test_final.tif fiji_test_hillshade.tif
```

This creates a georeferenced hillshade that can be used for visual inspection without opening an external GIS application.

Optional hillshade settings:

```bash
globato perspecto hillshade fiji_test_final.tif fiji_test_hillshade.tif --cmap etopo --exag 3 --blend soft_light
```

---

# What Does `-X 0:5` Do?

The `-X` option extends the processing region before gridding.

In this workshop, we use:

```bash
-X 0:5
```

This adds a 5 percent processing buffer around the specified output region.

For coastal DEM generation, the buffer can help reduce edge effects by including additional source data beyond the final DEM boundary. These surrounding data provide better support for interpolation near the edge of the requested output area.

The processing region is extended, but the final DEM remains associated with the requested output extent.

---

# Main Takeaway

`globato cudem build` defines a DEM workflow from command-line options.

`globato cudem build --export` writes that workflow to a reusable YAML recipe instead of running the DEM build.

`globato cudem run` executes a saved YAML recipe and generates the DEM outputs.

Together, these commands support a reproducible workflow:

```text
choose a region
→ select a source bundle
→ export the recipe
→ inspect the YAML
→ modify the workflow
→ run the recipe
→ inspect the outputs
```

`globato` is the CUDEM workflow engine that brings source modules, transformations, filtering, stacking, interpolation, and output generation together in one inspectable, recipe-based process.
