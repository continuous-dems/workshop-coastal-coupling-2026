# NOAA NCEI/CIRES Coastal DEM Workshop

## Open-Source Workflows for Coastal DEM Generation 

**Hosted by:** NOAA NCEI and CIRES for NOAA’s Coastal Coupling Community of Practice (CC CoP) Bathymetric Data Working Group  
**When:** Thursday, August 13, 2026, 2:00–4:00 p.m. Eastern  
**Where:** Virtual workshop — [Register here](https://docs.google.com/forms/d/e/1FAIpQLScjQHWOFzcscPURAiMVpfgfg4xOv8kWJ5v7WTDLpSqpA2F7OA/viewform)

## Workshop abstract

NOAA National Centers for Environmental Information (NCEI) and Cooperative Institute for Research in Environmental Sciences (CIRES) scientists will lead an interactive workshop on open-source tools for generating and evaluating coastal digital elevation models (DEMs).

Using a hosted JupyterHub environment, participants will work through a reproducible workflow for discovering, preparing, integrating, and validating topographic and bathymetric data. The workshop will introduce tools developed through the [Continuous DEMs project](https://github.com/continuous-dems) and demonstrate how they support transparent and adaptable coastal DEM production.

This workshop builds on the April 16, 2026 webinar, [“Open-Source Workflows for Coastal DEM Generation.”](https://www.weather.gov/media/watercommunity/Webinar/2026Apr16/Coastal%20Coupling%20Webinar_Amante_04162026.pdf)

## Workshop goals

By the end of the workshop, participants will be able to:

- Verify a working CUDEM software environment.
- Use `fetchez` to discover and access source datasets.
- Recognize how CUDEM source modules, processing hooks, and YAML recipe blocks are organized.
- Understand the role of `transformez` in vertical datum harmonization.
- Use `globato` to generate, export, run, and modify reproducible CUDEM recipes.
- Use `ivert` to independently validate DEM outputs.
- Inspect DEMs and supporting files with command-line tools.
- Identify the changes needed to adapt the workflow to another coastal region.

## Workshop structure

The workshop combines short demonstrations with guided, hands-on exercises:

1. **Environment and workflow introduction**
   - Verify access to the JupyterHub environment.
   - Review the coastal DEM production workflow and supporting tools.

2. **Data discovery and preparation**
   - Discover relevant elevation and bathymetric datasets.
   - Examine source metadata, spatial coverage, and reference systems.

3. **Recipe-based DEM generation**
   - Review the structure of a CUDEM YAML recipe.
   - Generate and run a coastal DEM workflow.
   - Modify the recipe for a new region or processing scenario.

4. **Output inspection and validation**
   - Inspect generated DEM products.
   - Run independent validation and review the results.
   - Discuss quality-control considerations and next steps.

## Main workflow sequence

| Tool | Role in the workflow |
|---|---|
| `fetchez` | Discover, access, describe, and prepare source data |
| `transformez` | Harmonize vertical reference systems |
| `globato` | Coordinate data sources, stacking, interpolation, and output generation |
| `ivert` | Independently validate DEM outputs |

