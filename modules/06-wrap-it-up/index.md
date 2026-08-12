---
title: "6 - Wrap it up"
subject: "Tutorial"
---

# 🏁 Wrap it up

A few final steps before logging off your virtual machine.

## 🔧 1. Delete your .netrc file

*If* you completed the optional [Building your own IVERT database](../05-evaluate-dems/building-an-ivert-database.md), then you probably have a `.netrc` file that contains your NASA Earthdata credentials. Go ahead and delete it:

```bash
rm ~/.netrc
```

## 🔧 2. Stop your server

**Download anything you want to keep first.** `~/workshop` points at `/tmp/workshop`, which is wiped when the server shuts down — DEMs, IVERT results, and all. Right-click any file in the JupyterLab file browser and choose Download.

When you're finished, please shut down your server so resources are released. We are charged for uptime on these virtual machines, and they stay "on" if you just close the browser.

See [Stopping your server](../../reference/02-stopping-your-server.md) for the steps.

## Want to learn more? Join the Continuous-DEMs community!

Have a dataset we should support?
Want to request a feature, suggest a new method, or ask a question?
Want to install these tools on your own computer but aren't sure how?

<p>
  <a href="https://cudem.zulipchat.com/">
    <img src="../../assets/images/zulip-icon-circle.svg" alt="" width="22" height="22" style="vertical-align: -0.28em; margin-right: 0.35em;">
    <strong>Join the CUDEM Zulip community</strong>
  </a>
</p>

We would love to hear how you are using the tools and what would make them more useful.

## Want to contribute code?

<p>
  <a href="https://github.com/continuous-dems">
    <img src="../../assets/images/mark-github-24.svg" alt="" width="22" height="22" style="vertical-align: -0.28em; margin-right: 0.35em;">
    <strong>Explore Continuous-DEMs on GitHub</strong>
  </a>
</p>

Continuous-DEMs is proudly open source, and we would love community contributions! Contributions can include:
- Identified bugs you've hit (we'd like to know!)
- Suggestions for improvements
- New code, bug fixes, or feature upgrades
- Enhanced documentation (or requests thereof)

If you are new to contributing to an open-source project, we are happy to help walk you through the process. Join the Zulip Community above and ask! The entire team is on there and we'd be happy to answer anything you need. **All contributions are welcome, big or small!**
