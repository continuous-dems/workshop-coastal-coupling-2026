---
title: "1 - Getting Started"
---

# Getting Started

Welcome to the **NOAA NCEI/CIRES Coastal DEM Workshop**.

Today we will use a hosted Openscapes JupyterHub environment to build and explore a high-resolution coastal DEM for **Newport, Oregon**, then reuse the same workflow for a second coastal DEM for **Sarasota, Florida** 

The workshop path is:

```text
get connected
      ↓
build the Newport DEM
      ↓
look inside the workflow
      ↓
explore the DEM
      ↓
reuse the recipe in Sarasota
      ↓
evaluate the DEMs
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

1. To login, enter any username you like. It must be unique from other participants, so consider using your full name, your email address, or your GitHub username.
2. Use the password shared in the chat.
3. Start your JupyterHub server.
4. Wait for **JupyterLab** to open.

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
| **Fetchez** | Orchestrate the workflow and discover and stream source data |
| **Transformez** | Support horizontal and vertical reference transformations |
| **Globato** | Provide elevation-specific tools for building the DEM |
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

If you have not worked in a Linux terminal before, these few commands will help you move around and see what is in your workspace.

## Where am I?

Run:

```bash
pwd
```

`pwd` means **print working directory**. It shows the folder you are currently working in.

## What is here?

Run:

```bash
ls
```

`ls` lists the files and directories in your current location.


This shows file sizes in an easier-to-read format.

## Move into a directory

Use `cd` followed by the directory name:

```bash
cd newport_dem
```

Then check where you are:

```bash
pwd
```

and what is there:

```bash
ls
```

## Move back one directory

Run:

```bash
cd ..
```

`..` means the **parent directory**, or one level up.

For example:

```text
home/newport_dem
        ↓
      cd ..
        ↓
home
```

## Return to your home directory

From anywhere, run:

```bash
cd ~
```

or simply:

```bash
cd
```

Both return you to your home directory.

:::{tip}
## A few useful terminal habits

**Use the Tab key for autocomplete**

Start typing a filename or directory name and press **Tab**. The terminal can often complete the rest for you.

For example:

```text
cd newp<Tab>
```

may complete to:

```text
cd newport_dem/
```

**Use the Up Arrow**

Press the **Up Arrow** to bring back commands you ran previously. This is useful when you want to make a small change and run a command again.

**Stop a running command**

If you need to stop a command that is currently running, press:

```text
Ctrl+C
```

Only do this when you intentionally want to stop that process.
:::

## Quick reference

| Command | What it does |
|---|---|
| `pwd` | Show your current directory |
| `ls` | List files and directories |
| `cd directory_name` | Move into a directory |
| `cd ..` | Move up one directory |
| `cd ~` or `cd` | Return to your home directory |
| `Tab` | Autocomplete a filename or directory |
| `Up Arrow` | Recall a previous command |
| `Ctrl+C` | Stop the currently running command |

:::{important}
You do **not** need to memorize these commands.

This page is here as a quick reference during the workshop.
:::


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

We will build a **1/9-arc-second** (~3-m spatial resolution) coastal DEM referenced to:

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
build the DEM
        ↓
inspect how the workflow used the data
        ↓
reuse the approach in another region
        ↓
evaluate the results

```

For Newport, we will use NOAA Digital Coast to find a local **topobathymetric lidar dataset** and add it to the national-scale recipe.

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

Give it another minute. If it does not open after ~5 minutes, let an instructor know in the chat.

**I cannot find Terminal**

Open the JupyterLab Launcher with the **+** button and look under **Other**.

**One of the version commands fails**

Do not install or update the package yourself during the workshop. Show the error to an instructor in the chat.

**I am not sure which directory I am in**

Run:

```bash
pwd
```

We will make sure everyone is in the correct location before starting the Newport build.
:::
