---
title: "1 - Getting Started"
---

# Getting Started

Welcome to the **NOAA NCEI/CIRES Coastal DEM Workshop**.

In this module, we will get everyone into JupyterLab, confirm that the workshop tools are ready, and set up the workspace we will use for the exercises.

> **Goal:** Get connected, open a terminal, confirm the tools, and prepare workshop data.

---

# 1. Open the workshop JupyterHub

Open the **[Coastal Coupling Workshop JupyterHub](https://workshop.nmfs-openscapes.2i2c.cloud/)**.

1. Enter a username that is unique from other participants.
2. Use the workshop password shared by the instructors in the chat.
3. Start your JupyterHub server.
4. Wait for **JupyterLab** to open.

For more detail on signing in and starting your server, see [Starting your server](../../reference/01-starting-your-server.md).

:::{important}
## Success check

You are ready for the next step when you can see the JupyterLab interface.
:::

---

# 2. Open a terminal

In JupyterLab:

1. Click the **+** button to open the Launcher.
2. Under **Other**, select **Terminal**.

You should now see a command prompt.

:::{tip}
New to the Linux terminal?

Keep the [Linux Terminal Quick Reference](../../reference/03-linux-terminal-quick-reference.md) open in another tab. You do **not** need to memorize Linux commands for this workshop.
:::

:::{important}
## Success check

You should be able to click in the terminal and see a blinking cursor at the command prompt.
:::

---

# 3. Confirm the workshop tools

The workshop environment already includes the four main tools we will use.

Run:

```bash
fetchez --version
transformez --version
globato --version
ivert --version
```

Each command should print a version number.

:::{dropdown} What are these tools?

| Tool | Role in the workshop |
|---|---|
| **Fetchez** | Orchestrate the workflow and discover and stream source data |
| **Transformez** | Support horizontal and vertical reference transformations |
| **Globato** | Provide elevation-specific tools for building the DEM |
| **IVERT** | Evaluate the DEM with independent observations |

We will see how these tools work together as we build and evaluate the DEMs.
:::

:::{important}
## Success check

If all four commands work, **do not install or update anything** during the workshop. The environment is already configured for the exercises.
:::

:::{dropdown} If a command is not found

Do not try to fix the environment with `pip install`.

Let an instructor know which command failed in the chat.
:::

---

# 4. Set up your workshop workspace

The workshop datasets have already been prepared so we can spend our time building and exploring DEMs rather than waiting for large downloads.

Run:

```bash
bash ~/shared/setup_workshop.sh
```

When setup finishes, check the workspace:

```bash
ls ~/workshop
```

You should see:

```text
ivert
newport_data
newport_dem
reference_outputs
sarasota_data
sarasota_dem
```

During the workshop, use `~/workshop` for the prepared data, DEM outputs, and reference outputs.

:::{dropdown} What is in each directory?

| Directory | Purpose |
|---|---|
| `~/workshop/newport_data` | Prepared Newport source-data cache |
| `~/workshop/sarasota_data` | Prepared Sarasota source-data cache |
| `~/workshop/ivert` | Prepared IVERT data used later for DEM evaluation |
| `~/workshop/reference_outputs` | Known-good Newport and Sarasota outputs available as workshop fallbacks |
| `~/workshop/newport_dem` | Your Newport DEM outputs |
| `~/workshop/sarasota_dem` | Your Sarasota DEM outputs |

:::

:::{dropdown} About temporary workshop storage

`~/workshop` points to `/tmp/workshop`, which is temporary storage attached to your JupyterHub server.

If your server is stopped and recreated, rerun:

```bash
bash ~/shared/setup_workshop.sh
```

Download or copy any DEM results you want to keep before ending your workshop server.
:::

:::{important}
## Success check

You are ready when the setup script finishes successfully and the directories listed above appear under `~/workshop`.
:::

---

:::{important}
# Ready for Newport?

Before moving on, confirm that:

- [ ] JupyterLab is open.
- [ ] You have a terminal open.
- [ ] All four tool version commands work.
- [ ] `bash ~/shared/setup_workshop.sh` completed successfully.
- [ ] `ls ~/workshop` shows the prepared workshop directories.

If those are all true, continue to **Module 2 — Build the Newport DEM**.
:::

:::{dropdown} Quick troubleshooting

**My JupyterHub server is still starting**

If it does not open after several minutes, let an instructor know in the chat.

**I cannot find Terminal**

Open the JupyterLab Launcher with the **+** button and look under **Other**.

**One of the version commands fails**

Do not install or update the package yourself during the workshop.

**The workshop setup script fails**

Do not manually move or rebuild the workshop data directories unless an instructor asks you to. Show the error in the chat.

**I am not sure which directory I am in**

See the [Linux Terminal Quick Reference](../../reference/03-linux-terminal-quick-reference.md).
:::
