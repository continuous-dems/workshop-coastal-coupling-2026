---
title: "1 - Getting Started"
---

# Getting Started

Welcome to the **NOAA NCEI/CIRES Coastal DEM Workshop**.

Today we will use a hosted Openscapes JupyterHub environment to build and explore a high-resolution coastal DEM for **Newport, Oregon**, then reuse the same workflow for a second coastal region.

The workshop path is:

```text
get connected
      ↓
build the Newport DEM
      ↓
look inside the workflow
      ↓
explore and evaluate the result
      ↓
reuse the recipe in Sarasota
```

The goal of this first module is simple:

> **Get everyone into JupyterLab, open a terminal, and confirm that the workshop tools are ready.**

---

# 1. Open the workshop JupyterHub

Open the JupyterHub link provided by the instructors.

<!-- IMAGE PLACEHOLDER
Suggested figure: screenshot of the workshop JupyterHub login / server launch page.
Caption: "Open the workshop JupyterHub and start your server."
-->

1. Sign in using the workshop instructions.
2. Start your JupyterHub server.
3. Wait for **JupyterLab** to open.

<!-- TODO: Add the exact server/profile name here once finalized.
Example wording:
"Choose the **[PROFILE NAME]** server option for this workshop."
-->

:::{tip}
Starting the server may take a few minutes.

We will introduce the workshop while everyone is connecting.
:::

:::{important}
## Success check

You are ready for the next step when you can see the JupyterLab interface.
:::

---

# 2. Open a terminal

We will run the workshop commands from a Linux terminal inside JupyterLab.

<!-- IMAGE PLACEHOLDER
Suggested figure: screenshot of the JupyterLab Launcher with Terminal highlighted.
Caption: "Open a Terminal from the JupyterLab Launcher."
-->

In JupyterLab:

1. Click the **+** button to open the Launcher.
2. Under **Other**, select **Terminal**.

You should now see a command prompt.

:::{important}
## Success check

You should be able to click in the terminal and see a blinking cursor at the command prompt.
:::

---

# 3. Confirm the workshop tools

The workshop environment already includes the main tools we will use:

| Tool | Role in the workshop |
|---|---|
| **Fetchez** | Discover and access elevation data |
| **Transformez** | Support horizontal and vertical reference transformations |
| **Globato** | Coordinate and build the coastal DEM |
| **IVERT** | Evaluate the DEM with independent observations |

Run:

```bash
fetchez --version
transformez --version
globato --version
ivert --version
```

:::{important}
## Success check

Each command should print a version number.

If all four commands work, **do not install or update anything** during the workshop. The environment is already configured for the exercises.
:::

:::{dropdown} If a command is not found

Do not try to fix the environment with `pip install`.

Let an instructor know which command failed so we can keep your workshop environment consistent with everyone else's.
:::

---

# 4. Get oriented in your workspace

Run:

```bash
pwd
ls
```

`pwd` shows your current working directory.

`ls` shows the files and directories available there.

You do not need to create the Newport DEM yet. We will start the build together in **Module 2**.

<!-- TODO: If participants need to change into a specific workshop directory,
add the exact `cd ...` command here.

Example:

```bash
cd [WORKSHOP_DIRECTORY]
```
-->

<!-- TODO: Add a one-line shared-cache check here if you want participants
to verify the staged workshop data before Module 2. Use the exact path that
will exist in the deployed JupyterHub environment. -->

---

# 5. What are we going to build?

Our main study area is **Newport, Oregon**, including Yaquina Bay, the entrance channel, nearby coastal waters, and adjacent topography.

<!-- IMAGE PLACEHOLDER
High-value figure: a map or screenshot showing the Newport workshop AOI.
Include the bounding box and enough coastline context to orient participants.

Suggested caption:
"Newport workshop study area around Yaquina Bay, Oregon."
-->

The region is:

```text
West:  -124.10
East:  -124.00
South:   44.59
North:   44.64
```

We will build an approximately **1/9-arc-second** coastal DEM using:

```text
Horizontal: NAD83
Vertical:   NAVD88
```

represented in the workflow as:

```text
epsg:4269+5703
```

You do not need to memorize these settings. They will appear in the build command in Module 2.

---

# 6. The workshop idea

We are not starting from a blank DEM recipe.

We will:

```text
start with strong nationally available coastal data
        ↓
look for useful local data
        ↓
add local knowledge where it improves the recipe
        ↓
build the DEM
        ↓
inspect how the workflow used the data
        ↓
evaluate the result
        ↓
reuse the approach in another region
```

For Newport, we will use NOAA Digital Coast to find a locally useful **topobathymetric lidar dataset** and add it to the national-scale recipe.

Later, we will repeat the same process in **Sarasota, Florida**.

---

:::{important}
# Ready?

Before moving on, confirm that:

- [ ] JupyterLab is open.
- [ ] You have a terminal open.
- [ ] `fetchez --version` works.
- [ ] `transformez --version` works.
- [ ] `globato --version` works.
- [ ] `ivert --version` works.

If those are all true, you are ready for **Module 2 — Build the Newport DEM**.
:::

---

:::{dropdown} Quick troubleshooting

**My JupyterHub server is still starting**

Give it another minute. If it does not open, let an instructor know.

**I cannot find Terminal**

Open the JupyterLab Launcher with the **+** button and look under **Other**.

**One of the version commands fails**

Do not install or update the package yourself during the workshop. Show the error to an instructor.

**I am not sure which directory I am in**

Run:

```bash
pwd
```

We will make sure everyone is in the correct location before starting the Newport build.
:::
