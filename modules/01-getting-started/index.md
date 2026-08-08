---

# Getting Started

Welcome to the **NOAA NCEI/CIRES Coastal DEM Workshop**.

During this workshop, we will use a hosted Openscapes JupyterHub environment to build, inspect, and evaluate a high-resolution coastal digital elevation model (DEM) for Newport, Oregon.

All of the software required for the workshop is already installed.

## Open JupyterHub

1. Open the workshop JupyterHub link provided by the instructors.
2. Enter the workshop login information.
3. Start your JupyterHub server.
4. Wait for JupyterLab to open.

{tip}
Starting the JupyterHub server may take a few minutes. We will introduce the workshop and Continuous DEMs while everyone is connecting.

# Open a terminal

In JupyterLab:

1. Click the **+** button to open the Launcher.
2. Under **Other**, select **Terminal**.

You should now have a Linux command-line terminal.

## Confirm the workshop tools

The workshop environment includes the primary Continuous DEMs tools:

- **Fetchez** — source-data discovery, access, and preprocessing
- **Transformez** — vertical datum transformations
- **Globato** — reproducible DEM-generation workflows
- **IVERT** — independent DEM evaluation using ICESat-2

You can confirm that the tools are available with:

```bash
fetchez --version
transformez --version
globato --version
ivert --version
```

## Workshop files

The Newport exercise uses a YAML recipe named:

```text
newport.yaml
```

The workflow will also use:

```text
shared_data/
```

for cached source data and:

```text
dems/
```

in your home directory for DEM processing and outputs.

For this workshop, approximately 5–10 GB of downloaded source data and supporting files have been staged in shared storage. This avoids having every participant download a separate copy of the same source data.

If a required source were not already available in the shared cache, the workflow would normally download it automatically.

Your processing and outputs will still be created in your own working directory.

## What are we going to build?

Our study area is **Newport, Oregon**, including the Yaquina Bay coastal region.

The region of interest is:

```text
West:  -124.10
East:  -124.00
South:   44.59
North:   44.64
```

We will generate a **1/9-arc-second coastal DEM** referenced horizontally to NAD83 and vertically to NAVD88.

During the workshop, we will follow the workflow from:

```text
source-data discovery
        ↓
source-specific processing
        ↓
horizontal and vertical standardization
        ↓
data weighting and stacking
        ↓
multiresolution interpolation
        ↓
final coastal DEM
        ↓
provenance and quality control
        ↓
independent ICESat-2 evaluation
```

:::{tip}
### Ready?

If you can see JupyterLab, open a terminal, and see `newport.yaml`, you are ready to continue.
:::
