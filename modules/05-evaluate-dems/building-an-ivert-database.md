---
title: "Building your own IVERT database"
subject: "Tutorial"
---

# 🗄️ Building your own IVERT database

:::{note}
This page is optional and is not part of the guided workshop. It takes approximately **20 minutes** to do on your own, and can take **an hour or more** for a larger region.
:::

The workshop uses a database of ICESat-2 granules that was prepared ahead of time and
placed on the shared drive. This page will cover building an equivalent database
yourself, for your own study area.

:::{tip} 🧭 Where we are going
:icon: false

In this tutorial we will build our own database of classified ICESat-2 photons, covering any region we choose.
Along the way we will meet NASA Earthdata and the Harmony subsetting service.
By the end we will be able to validate a DEM anywhere ICESat-2 flies, without depending on the shared workshop data.
:::

The workflow is:

```text
reset IVERT to your own directories
      ↓
set up NASA Earthdata credentials
      ↓
choose the region to cover
      ↓
download + classify ICESat-2 photons
      ↓
inspect the resulting database
      ↓
clear the download cache
```

---

## 🔧 1. Reset IVERT to your own directories

In Module 5 we pointed IVERT at the **shared** workshop database on the cloud drive. To build a database of your own, we first send IVERT back to its default directories in your home drive.

```bash
ivert options reset --yes
```

| Option | Meaning |
|---|---|
| `reset` | Restore all settings to IVERT's defaults by deleting your user config file |
| `--yes` | Skip the interactive confirmation prompt |

You can see where IVERT will now read and write with:

```bash
ivert options list
```

The two settings that matter here are:

| Setting | Default value |
|---|---|
| `ivert_database_directory` | `~/.ivert/database/granules` |
| `cache_directory` | `~/.ivert/cache` |

:::{note} Getting the workshop database back
:icon: false

Resetting discards the pointer to the shared workshop database. If you want it back later, just re-run the command from Module 5:

```bash
ivert options ivert_database_directory=~/shared/ivert/granules cache_directory=~/shared/ivert/cache --yes
```

Nothing is deleted by the reset — both databases can coexist, and you switch between them by changing this one setting.
:::

---

## 🔧 2. Set up your NASA Earthdata credentials

IVERT gets its data from **NASA Earthdata**, which requires a free account.

1. If you don't already have one, create an account at [NASA Earthdata user account setup](https://urs.earthdata.nasa.gov/users/new).
2. If you already have an account, retrieve your existing username and password.

Copy your Earthdata username and password somewhere handy — you'll use them in a moment.

Now run IVERT's setup command, which creates the local data directories and checks for your credentials:

```bash
ivert setup
```

If IVERT does not find Earthdata credentials already stored, it will prompt you for them and offer to save them.

:::{tip}
Credentials are saved to a `~/.netrc` file in your home directory, so **you only have to enter them once**. Every later IVERT download will read them from there.

If you skip this step, the download command in the next section will prompt you for the same credentials instead.
:::

:::{important} 🧪 Success check
:icon: false

`ivert setup` completes without an error and reports that your NASA Earthdata credentials are in place.
:::

---

## 🔧 3. Choose the region to cover

An IVERT database is built to cover a **geographic region**. The easiest way to define that region is to hand IVERT the DEM you eventually want to validate — IVERT reads its extent directly.

Move into the directory holding that DEM:

```bash
cd ~/sarasota_dem
```

:::{note}
Use another directory here if you have a different DEM you'd like to validate. Everything below works the same way — only the extent changes.
:::

---

## 🔧 4. Download the ICESat-2 data

From that directory, run:

```bash
ivert database download --date-start 2023.10.01 --date-end 2024.11.01 *_final.tif
```

| Option | Meaning |
|---|---|
| `--date-start 2023.10.01` | Start date for the ICESat-2 search |
| `--date-end 2024.11.01` | End date for the search |
| `*_final.tif` | The DEM(s) whose combined extent defines the download region |

:::{tip} How much data should I grab?
:icon: false

We generally recommend grabbing **at least a year** of ICESat-2 data to perform a good validation. ICESat-2 repeats its orbits every 90 days (and cannot collect through clouds), so a shorter window can leave large parts of your DEM unsampled.
:::

:::{warning} Why the November 2024 end date?
:icon: false

ICESat-2's **ATL24** data product (bathymetry classification) is only processed periodically, and as of **August 2026** it only has data through **November 7, 2024**. NASA will process newer data this fall 2026, but for now we recommend requesting photons **on or before November 2024** to get the best chance of having bathymetry classifications in a coastal area.

IVERT knows this cutoff — it is the `atl24_date_cutoff` setting — and will warn you interactively if your date range extends past it.

If your DEM is **not** near a coastline, none of this matters and you can request any date range you like.

*Of note:* ICESat-2 began collecting data on October 13, 2018, and is still going strong in August 2026.
:::

If prompted, enter your NASA Earthdata login info (unless you already saved it in step 2).

:::{dropdown} Prefer to specify a bounding box instead of a DEM?

`ivert database download` also accepts a 4-value bounding box in **W/E/S/N** order:

```bash
ivert database download --date-start 2023.10.01 --date-end 2024.11.01 -- -82.7/-82.5/27.3/27.5
```

Note the `--` delimiter. It explicitly ends the command-line options, which is required whenever your coordinates begin with a negative `-`.

Add `--wsen` if you would rather give the box in W/S/E/N (lower-left, upper-right) order.
:::

### What is actually happening

This sends a request to the **NASA Harmony** cloud service, which subsets your ICESat-2 granules *before* downloading them, saving you many GB of downloads. IVERT then downloads the ancillary data products needed to complete the photon classifications.

:::{tip}
It's probably a good idea to grab a cup of coffee or check your email while this runs.

It usually takes about **20 minutes** for short jobs like this, and can take **more than an hour** for larger queries, especially if the NASA Harmony server is busy.
:::

---

## 🔧 5. Check your database

When the classification is complete, look at what you have:

```bash
ivert database list
```

You should see one row per granule, something like this (this example is from a small Newport, Oregon region):

```text
File                                                                   Total    Ground    BathyFloor    BathySurf
--------------------------------------------------------------------  -------  --------  ------------  -----------
ATL03_20231028200109_06082106_007_01_subsetted_..._20231001_20241101.nc  52,160       197            19            0
ATL03_20231124062932_10122102_007_01_subsetted_..._20231001_20241101.nc  24,434         0             3            6
ATL03_20240223020859_10122202_007_01_subsetted_..._20231001_20241101.nc  12,512         0             0            1
```

The columns tell you whether the download was useful:

| Column | Meaning |
|---|---|
| `Total` | All photons retained in that granule |
| `Ground` | Photons classified as ground returns (ATL08) — these validate the **terrestrial** part of your DEM |
| `BathyFloor` | Seafloor photons (ATL24) — these validate the **submerged** part of your DEM |
| `BathySurf` | Water-surface photons |

The granule filenames are informative too:

```text
ATL03_20231028200109_06082106_007_01_subsetted_W124.10019_W123.99981_N44.58981_N44.64019_20231001_20241101.nc
  │          │           │     │  │              │                                          │
  │          │           │     │  │              └── bounding box you requested             └── date range you requested
  │          │           │     │  └── revision
  │          │           │     └── product version
  │          │           └── orbit / cycle / segment identifiers
  │          └── satellite acquisition date and time
  └── source product
```

You can also check how much disk space the database is using:

```bash
ivert database size
```

:::{important} 🧪 Success check
:icon: false

Ask yourself:

1. Are there files in the database at all?
2. Do they have classified **ground** photons in them?
3. If your DEM is coastal, do any of them have **bathymetry** photons (`BathyFloor`)?

If the list comes back empty, the download either found no overlapping granules or did not complete. Re-check your date range and your DEM's extent.

If you have granules but every `BathyFloor` count is `0`, that is not necessarily an error — clear, shallow water is a prerequisite for ICESat-2 bathymetry, and many coastlines simply don't have it.
:::

---

## 🔧 6. Check and clear your cache

Downloading happens in two stages. IVERT first pulls **raw** ICESat-2 granules and ancillary data products into a **cache** directory, then writes the classified, subsetted photons out to the database.

That cache is scratch space, and it is often **100x larger than the database it produced**. Once the database has been built successfully, you can reclaim that space.

Check the size first:

```bash
ivert cache list
```

The `icesat2` subdirectory is nearly all of this disk-space, holding the raw granules (including noise photons) that have already been subsetted and classified into your database.

To clear it:

```bash
ivert cache delete
```

:::{warning}
Only clear the cache **after** you have confirmed your database looks right in step 5.

Deleting the cache does not touch your database — the classified photons are already saved. But the cache also holds reusable ancillary files (`proj` conversion grids, the `bing` building mask, the `osm_landmask`), and IVERT will re-download or rebuild those the next time if it needs them.
:::

:::{important} 🧪 Success check
:icon: false

`ivert cache list` reports a `TOTAL` of 0 files, while `ivert database size` still reports all of your `.nc` granules.

Your database is intact and you reclaimed your scratch space.
:::

---

## 🔧 7. Validate against your own database

Your new database is now IVERT's default, so validation works exactly as it did in Module 5 — no extra flags needed:

```bash
ivert validate *_final.tif -n "Sarasota, FL" --overwrite
```

The results land in an `ivert_results` sub-directory, with the same five output files described in [Module 5](./index.md).

Note: the `--overwrite` flag just tells IVERT to ignore any previous IVERT results in the subdirectory, and overwrite them with fresh results.

---

:::{important} The takeaway
:icon: false

Building your own database is the step that makes IVERT portable to **your** work:

> **Any DEM, anywhere ICESat-2 orbits, can be checked against independent observations.**

The three things to get right are:

1. **Extent** — cover the DEM you actually want to validate
2. **Time span** — at least a year, and mind the ATL24 cutoff for coastal work
3. **Content** — confirm with `ivert database list` that you have classified photons, not just granules

And once it's built, clear the cache. The database is the durable product; the multi-gigabyte download cache is not.

Once the database exists, it is reusable. Any number of DEMs overlapping that region can be validated against it without downloading anything again.

If you want to download new areas or a greater time-span, you can make more requests. IVERT will only download areas and time-spans that have not been requested. It will automatically subset your new areas if they partially overlap previous requests, so you will know that your area contains a full record of all the areas and dates you requested, *without duplicate granules.*
:::

---

## Where to go next

- Return to **[5 - Evaluate the DEMs with IVERT](./index.md)** for the validation workflow and how to interpret the outputs.
- Run `ivert classes` to see the full list of photon classification codes, which you can select with the `-c/--classes` option at download time.
- Run `ivert database export` to write your photons out to GeoPackage, Shapefile, or XYZ for use in a GIS or another workflow.
- Every command has detailed help: `ivert <command> --help`.
