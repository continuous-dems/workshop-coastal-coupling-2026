# 🚀 Starting your server

TODO: Edit for Openscapes hub

Everything in this workshop will be done on your own personal server on
[the 2i2c NMFS workshops JupyterHub](https://workshop.nmfs-openscapes.2i2c.cloud/).
It provides a computer in the cloud that's set up with all the necessary software that
you'll need for this workshop plus a familiar JupyterLab interface to use that computer.

:::{hint}
Your server's disk space is persistent, meaning that any files you create will still
exist after you stop and restart your server.
:::


## Login

To login, enter any username you like.
**It must be unique from other participants**, so consider using your full name, your
email address, or your GitHub username.

The workshop password will be shared with you on the day of the workshop.


## Server options

Once you're logged in to [the workshop JupyterHub](https://workshop.nmfs-openscapes.2i2c.cloud/),
you'll be presented with a screen like this:

![](/assets/images/jupyterhub-server-options.jpg)

The default settings are correct, you don't need to change anything.

:::{note} Some under-the-hood details
:class: dropdown

For reference, the default image is
`ghcr.io/continuous-dems/cudem-jupyter:workshop-coastal-coupling-2026`.

You can change it to any Docker image you like using the "Image" dropdown.
:::


## Click "Start"

When you click start, JupyterHub will begin creating your personal server.
You should see a progress bar like this:

![](/assets/images/jupyterhub-server-starting.png)

After a few moments, you'll be presented with the JupyterLab interface.

:::{important}
It's critical to only allocate the resources (memory and CPU) you need to keep this
workshop costs low so we can keep doing more workshops like this!

If you find your analysis requires more resources than you allocated, you can
[stop your server](./02-stopping-your-server.md) and recreate it with more resources.

It's also important to [stop your server](./02-stopping-your-server.md) when you're not
using it.
:::
