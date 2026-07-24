# 🚀 Starting your server

TODO: Edit for Openscapes hub

Everything in this workshop will be done on your own personal server on
[the CryoCloud JupyterHub](https://hub.cryointhecloud.com/).

**Your server's disk space is persistent, meaning that any files you create will still
exist after you stop and restart your server.**


## Server options

Once you're logged in to [the CryoCloud JupyterHub](https://hub.cryointhecloud.com),
you'll be presented with a screen like this:

![](/assets/images/cryocloud-server-options.jpg)

Instead of filling out this form,
[click here to open the same page with the form pre-filled with the correct options](https://hub.cryointhecloud.com/hub/spawn#fancy-forms-config={%22profile%22:%22cpu-only%22,%22image%22:%22unlisted_choice%22,%22image:unlisted_choice%22:%22ghcr.io/continuous-dems/cudem-jupyter:workshop-gebco-2026%22,%22resource_allocation%22:%22mem_7_gb%22,%22resource_allocation:unlisted_choice%22:%22%22}).

For reference, the correct values are:

* **Environment**: "Other..."
* **Custom image**: `ghcr.io/continuous-dems/cudem-jupyter:workshop-coastal-coupling-2026`
* **Resource allocation**: "~7GB RAM, ~0.9 CPUs"

:::{important}
It's critical to only allocate the resources you need so that CryoCloud can sustain its
operations!

If you find your analysis requires more resources than you allocated, you can
[stop your server](./02-stopping-your-server.md) and recreate it with more resources.

It's also important to [stop your server](./02-stopping-your-server.md) when you're not
using it.
:::


## Click "Start"

When you click start, CryoCloud will begin creating your personal server.
You should see a progress bar like this:

![](/assets/images/cryocloud-server-starting.png)

After a few moments, you'll be presented with the JupyterLab interface.
