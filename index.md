# NOAA NCEI/CIRES Coastal DEM Workshop -- NOAA's Coastal Coupling Community of Practice (CC CoP) Bathymetric Data Working Group

All workshop materials are available on this website.

**What:** Generating Coastal DEMs: From Data Discovery to Validation

**When:** Thursday, August 13, 2026: 2:00pm to 4:00pm Eastern

**Where:** TODO

TODO: Edit this whole thing :)

## Workshop abstract

NOAA NCEI and CIRES scientists will host an interactive, hands-on workshop introducing open-source tools for generating and evaluating coastal digital elevation models (DEMs). Participants will explore reproducible workflows in a JupyterHub environment for discovering, processing, integrating, and validating topographic and bathymetric data using tools from the Continuous DEMs project: https://github.com/continuous-dems.  

This hands-on workshop builds on their April 16, 2026 webinar titled "Open-Source Workflows for Coastal DEM Generation." 

## Workshop goals

By the end of the workshop, participants should be able to:

- Set up and verify a working CUDEM software environment
- Use `fetchez` to discover and access source datasets
- Understand how CUDEM source modules, hooks, and YAML recipe blocks are structured
- Use `transformez` to understand vertical datum harmonization
- Use `globato` to generate, export, run, and modify CUDEM recipes
- Use `ivert` to validate DEM outputs
- Inspect DEM outputs using command-line tools
- Adapt a workflow to a new coastal region

## Workshop structure

TODO

## Main workflow sequence

```text
fetchez      → discover, access, describe, and prepare source data
transformez  → harmonize vertical references
globato      → synthesize sources, hooks, transformations, stacking, interpolation, and outputs
ivert        → independently validate DEM outputs
```
