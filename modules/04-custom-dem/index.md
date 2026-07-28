---
title: "10 - Custom Region DEM"
---

After working through `fetchez`, `transformez`, and `globato`, this final Day 2 exercise brings the workflow together by building a DEM for a custom coastal region. You will choose a small coastal area, apply the same `globato` build pattern used for the Fiji example, and review the resulting output for basic quality-control issues.

---

# Export the Custom Recipe

Choose a small coastal region and apply the same workflow pattern used for the Fiji DEM.

General command:

```bash
globato cudem build -R WEST/EAST/SOUTH/NORTH -E 3s -O custom_test -P epsg:4326+3855 -M ms_binary_cudem:algos=interp_gmt -X 0:5 global-bathy-topo --export
```

Replace:

```text
WEST/EAST/SOUTH/NORTH
```

with the selected coastal bounding box.

Example centered on the Nukuʻalofa, Tonga region:

```bash
globato cudem build -R -175.40/-175.00/-21.35/-21.00 -E 3s -O custom_test -P epsg:4326+3855 -M ms_binary_cudem:algos=interp_gmt -X 0:5 global-bathy-topo --export
```

The `--export` option writes the generated YAML workflow to a recipe file instead of running the full DEM build.

Expected recipe output:

```text
custom_test_recipe.yaml
```

Open the exported recipe in a text editor and compare it with the Fiji recipe from Day 1.

---

# Run the Custom Recipe

Run the exported recipe to generate the DEM:

```bash
globato cudem run custom_test_recipe.yaml
```

Expected final DEM:

```text
custom_test_final.tif
```

---

# Inspect the Output

Inspect the custom DEM the same way you inspected the Fiji output in the `globato` module:

```bash
gdalinfo custom_test_final.tif
```

## Create a color shaded-relief image:

```bash
globato perspecto hillshade --help
```

Example hillshade command:

```bash
globato perspecto hillshade INPUT_DEM.tif OUTPUT_HILLSHADE.tif --cmap etopo --exag 3 
```

---

# Basic Output Review

Review the generated DEM and hillshade using the following questions:

- Does the DEM cover the expected region?
- Are land and water in the correct locations?
- Are land elevations generally positive?
- Are underwater elevations generally negative?
- Are there missing-data areas or obvious gaps in source coverage?
- Are there edge or coastline artifacts?
- Are there interpolation artifacts or unexpected surface patterns?
- Does the hillshade show reasonable terrain and bathymetric structure?
- Do neighboring source datasets appear consistent?
- Are there areas that should be checked against local or independent data?

This is not a complete validation procedure. The goal is basic output review and quality-control thinking about the relationship between source data, workflow settings, and DEM quality, using tools available within the workshop environment.

---

# Modify One Thing

After the first custom run, modify one part of the command or recipe.

## Change the Region

Export a new recipe using a different bounding box:

```bash
globato cudem build -R WEST/EAST/SOUTH/NORTH -E 3s -O custom_region_v2 -P epsg:4326+3855 -M ms_binary_cudem:algos=interp_gmt -X 0:5 global-bathy-topo --export
```

Then run it:

```bash
globato cudem run custom_region_v2_recipe.yaml
```

## Change the Resolution

Export a 1-arc-second version:

```bash
globato cudem build -R 178.25/178.65/-18.30/-17.95 -E 1s -O fiji_test_1s -P epsg:4326+3855 -M ms_binary_cudem:algos=interp_gmt -X 0:5 global-bathy-topo --export
```

Run the exported recipe:

```bash
globato cudem run fiji_test_1s_recipe.yaml
```

Alternatively, override the saved Fiji recipe:

```bash
globato cudem run fiji_test_recipe.yaml -E 1s -O fiji_test_1s
```

Compare the outputs:

```bash
gdalinfo fiji_test_final.tif
gdalinfo fiji_test_1s_final.tif
```

The goal is not to produce a perfect DEM. The goal is to understand how changes to the region, resolution, sources, and workflow settings affect the resulting output.

---

# Wrap-Up and Next Steps

By the end of Day 2, participants should have:

- Reactivated the `cudem` environment
- Reviewed the Fiji test DEM
- Seen how `fetchez`, `transformez`, `globato`, and IVERT support the CUDEM workflow
- Explored source modules and YAML formatting
- Reviewed the role of vertical harmonization
- Generated or inspected a custom DEM recipe
- Reviewed DEM outputs for basic quality-control issues
- Understood how the Fiji recipe can be adapted to other coastal regions

## Suggested Next Steps

- Explore additional source modules and module bundles
- Inspect additional example recipes
- Try a new coastal region
- Compare different output resolutions
- Add local or project-specific source data
- Begin adapting the workflow to your own project area
- Use IVERT or other independent data to assess the resulting DEM
- Continue using Zulip for questions, support, and sharing workflow ideas

[Continuous DEMs Zulip](https://cudem.zulipchat.com)
