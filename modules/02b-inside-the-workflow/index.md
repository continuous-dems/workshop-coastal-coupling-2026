---
title: "2b - Inside the Workflow"
---

# Inside the Workflow

The Newport DEM should now be running.

Keep the terminal visible as we walk through the major stages of the workflow.

You do **not** need to interpret every message. Instead, try to recognize this overall sequence:

```text
discover data
      ↓
prepare each source
      ↓
standardize reference systems
      ↓
prioritize and stack measurements
      ↓
interpolate across multiple scales
      ↓
write the final DEM
```

:::{tip}
## Follow the workflow, not every log line

As the build runs, periodically ask:

> **Which stage of the workflow are we seeing right now?**

That is more useful than trying to understand every terminal message.
