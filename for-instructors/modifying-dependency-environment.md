# 🔧 Modifying the dependency environment

The Docker image we use comes with dependencies pre-installed.
To modify the environment, you must push to the
[continuous-dems/docker-jupyter](https://github.com/continuous-dems/docker-jupyter)
repository.

## Edit the dependency manifest

Edit `pixi.toml` to add, remove, or modify dependencies in the environment.

:::{important}
Run `pixi lock` to automatically update `pixi.lock`!!!
:::

Commit.

## Push the commit

Push the new commit:

```bash
git push origin main
```

:::{note}
You may want to use a pull request workflow instead of pushing to main.
If you do this, wait for merge to perform the next step.
:::

## Update the workshop tag

From the latest commit on `main` (you may want to `git pull` first):

```bash
git tag --force workshop-coastal-coupling-2026
```

Push the tag:

```bash
git push --force origin workshop-coastal-coupling-2026
```

## Watch the build

Watch the build here:

<https://github.com/continuous-dems/docker-jupyter/actions>

It should take 4 minutes or so.

## See the published package

The final package should appear here:

<https://github.com/continuous-dems/docker-jupyter/pkgs/container/cudem-jupyter>
