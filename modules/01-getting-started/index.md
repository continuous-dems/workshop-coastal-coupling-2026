---
title: "1 - Getting Started"
---
# Getting Started

Welcome to the **NOAA NCEI/CIRES Coastal DEM Workshop**.

Today we will use a hosted Openscapes JupyterHub environment to build and explore a high-resolution coastal DEM for **Newport, Oregon**, then reuse the same workflow for a second coastal DEM for **Sarasota, Florida**.

The goal of this first module is simple:

> **Get everyone into JupyterLab, open a terminal, confirm that the workshop tools are ready, and set up the workshop workspace.**

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

Choose the workshop server option specified by the instructors.

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
# 4. Set up your workshop workspace

The large workshop datasets are stored as prepared archives in cloud storage. A setup script copies them into a fast, participant-specific workspace for this session.

Run this one command:

```bash
bash ~/shared/setup_workshop.sh
```

The script will:

- use the AWS CLI already available in the workshop image, or install it for your user as an emergency fallback if needed;
- download the prepared Newport, Sarasota, IVERT, and reference-output archives;
- extract them into `/tmp/workshop`;
- create `~/workshop` as the stable path you will use throughout the tutorial; and
- create the Newport and Sarasota DEM output directories.

You do **not** need to work directly inside `~/shared`. During the workshop, use `~/workshop` for the data, caches, reference outputs, and DEM results.

When setup finishes, check the workspace:

```bash
ls -ld ~/workshop
ls ~/workshop
```

You should see these directories:

```text
ivert
newport_data
newport_dem
reference_outputs
sarasota_data
sarasota_dem
```

Their roles are:

| Directory | Purpose |
|---|---|
| `~/workshop/newport_data` | Prepared Newport source-data cache used by Globato |
| `~/workshop/sarasota_data` | Prepared Sarasota source-data cache used by Globato |
| `~/workshop/ivert` | Prepared IVERT data used later for DEM evaluation |
| `~/workshop/reference_outputs` | Known-good Newport and Sarasota outputs available as workshop fallbacks |
| `~/workshop/newport_dem` | Your Newport DEM outputs |
| `~/workshop/sarasota_dem` | Your Sarasota DEM outputs |

:::{important}
## Temporary workshop storage

`~/workshop` points to `/tmp/workshop`, which is temporary storage attached to your JupyterHub server.

If your server is stopped and recreated, rerun:

```bash
bash ~/shared/setup_workshop.sh
```

Download or copy any DEM results you want to keep before ending your workshop server.
:::

:::{important}
## Success check

You are ready when this command works:

```bash
ls ~/workshop/newport_data \
   ~/workshop/sarasota_data \
   ~/workshop/ivert \
   ~/workshop/reference_outputs/newport \
   ~/workshop/reference_outputs/sarasota
```

and `~/workshop/newport_dem` and `~/workshop/sarasota_dem` both exist.
:::

---
# 5. Get oriented in your workspace

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
ls ~/workshop
```

`ls` lists files and directories. Here we are explicitly asking it to show the contents of your workshop workspace.

## Move into a directory

Use `cd` followed by the directory path. For example:

```bash
cd ~/workshop/newport_dem
```

Then check where you are:

```bash
pwd
```

and what is there:

```bash
ls
```

The Newport output directory may be empty right now. We will create the DEM together in **Module 2**.

## Move back one directory

Run:

```bash
cd ..
```

`..` means the **parent directory**, or one level up.

For example:

```text
~/workshop/newport_dem
          ↓
        cd ..
          ↓
~/workshop
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
cd ~/workshop/newp<Tab>
```

may complete to one of the Newport directories.

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

---
# 6. What are we going to build?

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
# 7. The workshop idea

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
- [ ] `bash ~/shared/setup_workshop.sh` completed successfully.
- [ ] `~/workshop/newport_data` exists.
- [ ] `~/workshop/sarasota_data` exists.
- [ ] `~/workshop/ivert` exists.
- [ ] `~/workshop/reference_outputs` exists.

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

**The workshop setup script fails**

Copy the error message from the terminal and show it to an instructor. Do not manually move or rebuild the workshop data directories unless an instructor asks you to.

**I am not sure which directory I am in**

Run:

```bash
pwd
```

You can always inspect the workshop workspace with:

```bash
ls ~/workshop
```

We will make sure everyone is ready before starting the Newport build.
:::
