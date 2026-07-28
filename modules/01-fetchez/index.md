---
title: "7 - Data Discovery with fetchez"
---

# Module Overview: Data Discovery with `fetchez`

`fetchez` is the CUDEM data discovery and access tool. It helps users query, filter, cache, stream, and download source datasets through a standardized command-line interface.

In a coastal DEM workflow, `fetchez` helps answer questions such as:

- What source data are available in this region?
- Which datasets overlap the area of interest?
- Can the results be filtered by source type, year, provider, or data class?
- Can only the required data be downloaded?
- How should a source and its processing settings be represented in YAML?

Run the basic help command:

```bash
fetchez --help
```

List available source modules:

```bash
fetchez modules list
```

Inspect the module commands:

```bash
fetchez modules --help
```

Search available modules by keyword:

```bash
fetchez modules search topography
fetchez modules search bathymetry
```

Inspect a specific source module:

```bash
fetchez modules info mbdb
```

The main concept is that data discovery should be simple and programmatically accessible. Instead of manually downloading files from many different websites, `fetchez` provides a common interface for accessing more than 75 source types as part of a larger workflow or as a standalone data-acquisition tool.

The `fetchez` exercise also introduces the YAML structure used to represent source modules, module arguments, hooks, weights, and cache settings. These same components later appear inside the workflows coordinated by `globato`.

---

`fetchez` is organized around a few core concepts:

```text
fetchez
fetchez modules
fetchez hooks
fetchez recipes
fetchez streams
```

The most useful starting point is the main help command:

```bash
fetchez --help
```

This shows the main components of the `fetchez` workflow: modules, hooks, streams, and recipes.

---

# Explore Available Data Modules

`fetchez` modules define source-data access. Each module represents a dataset, service, or local data type that can be used in a CUDEM workflow.

Search modules by keyword:

```bash
fetchez modules search topography
```

```bash
fetchez modules search bathymetry
```

These searches help identify source modules relevant to a particular data theme or workflow.

---

# Example: Find and Download Data

For many users, the most common use of `fetchez` is straightforward data access: find data for a region and download it into a working directory.

The general command pattern is:

```bash
fetchez run -R WEST/EAST/SOUTH/NORTH MODULE_NAME
```

For example, to request NOAA multibeam data from the `mbdb` module for the Fiji test region:

```bash
fetchez run -R 178.25/178.65/-18.30/-17.95 mbdb
```

This command tells `fetchez` to use the `mbdb` module and search for matching multibeam data within the specified bounding box.

The region format is:

```text
WEST/EAST/SOUTH/NORTH
```

For the Fiji example:

```text
178.25/178.65/-18.30/-17.95
```

---

# Inspect a Specific Module

Use `fetchez modules info` to inspect a module definition:

```bash
fetchez modules info mbdb
```

The `mbdb` module accesses NOAA multibeam data through an ArcGIS Feature Server.

The module information includes:

- Description
- Category
- Agency
- Resolution
- Tags
- Links
- Available arguments

The figure below shows the command output for:

```bash
fetchez modules info mbdb
```

The output summarizes the module and lists the arguments that can be used to control the request for the selected dataset.

These arguments can be used directly in a command-line pipeline or written into a YAML recipe.

---

## Limit the Request by Year

Some modules support arguments that narrow the data request.

For the `mbdb` module, a request can be limited by year:

```bash
fetchez run -R WEST/EAST/SOUTH/NORTH mbdb --min-year YEAR --max-year YEAR
```

Example:

```bash
fetchez run -R 178.25/178.65/-18.30/-17.95 mbdb --min-year 2010 --max-year 2024
```

This searches the selected region for multibeam data within the specified year range.

---

# Explore Processing Hooks

Hooks are processing steps that prepare source data before they are used in a workflow.

Common hook operations include:

- Setting or identifying the source data type
- Assigning the source reference system
- Reprojecting source data horizontally
- Cropping data to the target region
- Filtering elevation or depth values
- Comparing data against a reference surface
- Adding weights, metadata, or provenance information

Inspect the hooks command:

```bash
fetchez hooks --help
```

List available hooks:

```bash
fetchez hooks list
```

Get information about a specific hook:

```bash
fetchez hooks info stream_reproject
```

Another useful example is:

```bash
fetchez hooks info rq
```

The `stream_reproject` hook is used to reproject source data into a target horizontal reference frame.

The `rq` hook supports reference-based filtering, where source values are evaluated against a comparison surface.

Hook arguments can be used directly in a command-line pipeline or written into a YAML recipe.

---

# Hooks in YAML

A source module can include one or more hooks inside a YAML recipe.

Example:

```yaml
modules:
  - module: mbdb
    args:
      weight: 1
      want_inf: true
    hooks:
      - name: stream_reproject
        args:
          dst_srs: epsg:4326+3855
      - name: spatial_crop
      - name: rq
        args:
          reference: gmrt/gebco
          threshold: 10
          mode: percent
          builder: grid
          res: 0.000277777
```

The module defines the source-data access. The hooks define how the source is prepared before it enters the larger workflow.

---

# Explore Recipes

Recipes are YAML files that define preassembled workflows.

Inspect the recipes command:

```bash
fetchez recipes --help
```

List available recipes:

```bash
fetchez recipes list
```

Print a recipe directly to the terminal:

```bash
fetchez recipes dump RECIPE_NAME
```

Validate a recipe:

```bash
fetchez recipes validate fiji_test_recipe.yaml
```

Recipe commands are useful for understanding how source modules, arguments, hooks, weights, and processing settings are represented in YAML.

---

# Main Takeaway

At its simplest, `fetchez` can be used to find and download source data:

```text
select a module
→ define a region
→ narrow the request
→ download or stream the data
```

Its more advanced features expose the structure used by the larger CUDEM workflow:

```text
modules
→ module arguments
→ hooks
→ hook arguments
→ streams
→ YAML recipes
```

This exercise provides a view of the individual source-data components that `globato` later synthesizes into a complete, recipe-based DEM workflow.
