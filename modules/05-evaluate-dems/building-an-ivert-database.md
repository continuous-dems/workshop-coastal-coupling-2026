---
title: "Building your own IVERT database"
subject: "Tutorial"
---

# 🗄️ Building your own IVERT database

:::{note}
This page is optional and is not part of the guided workshop. It takes approximately **20 minutes** on your own, and an hour or more for a larger region.
:::

The workshop uses a database of ICESat-2 granules prepared ahead of time and staged in `~/workshop/ivert`. Here we build an equivalent database for a study area of your own, so you can validate a DEM anywhere ICESat-2 flies.

---

## 🔧 1. Point IVERT at a database directory of your own

The prepared `~/workshop/ivert` database is workshop reference data, so we point IVERT at a fresh, writable directory instead:

```bash
ivert options ivert_database_directory=~/workshop/ivert_database/granules cache_directory=~/workshop/ivert_database/cache --yes
```

| Option | Meaning |
|---|---|
| `ivert_database_directory=...` | Where IVERT writes your new granules |
| `cache_directory=...` | Where raw downloads and conversion grids go |
| `--yes` | Skip the interactive confirmation prompt |

This is a **different** directory from the prepared `~/workshop/ivert` database — nothing here touches the workshop copy. Check the current settings any time with `ivert options list`.

:::{dropdown} Switching back to the workshop database

Re-run the command from Module 5:

```bash
ivert options ivert_database_directory=~/workshop/ivert/granules cache_directory=~/workshop/ivert/cache --yes
```

Nothing is deleted by switching — both databases can coexist, and you move between them by changing this one setting.
:::

:::{dropdown} ⚠️ `~/workshop` is temporary storage

`~/workshop` points at `/tmp/workshop`, which does not survive your JupyterHub server being stopped and recreated — the database you are about to spend 20+ minutes building included. Do this in a session you keep open, and download anything you want to keep before you finish. If you do log back in later, re-run `bash ~/shared/setup_workshop.sh` to re-stage the workshop data.

On your own machine, point these settings anywhere you like — or run `ivert options reset --yes` to return to IVERT's defaults under `~/.ivert`.
:::

---

## 🔧 2. Set up your NASA Earthdata credentials

IVERT gets its data from **NASA Earthdata**, which requires a free account. Create one at [NASA Earthdata user account setup](https://urs.earthdata.nasa.gov/users/new) if you don't have one, then run:

```bash
ivert setup
```

This creates the local data directories and checks for your credentials, prompting you for them if it doesn't find any.

:::{important} 🧪 Success check
:icon: false

`ivert setup` completes without an error and reports that your NASA Earthdata credentials are in place.
:::

:::{dropdown} Where do the credentials go?

They are saved to a `~/.netrc` file in your home directory, so **you only enter them once** — every later IVERT download reads them from there. If you skip this step, the download command in the next section prompts you for the same credentials instead.
:::

---

## 🔧 3. Choose the region and download

An IVERT database covers a **geographic region**. The easiest way to define it is to hand IVERT the DEM you want to validate — it reads the extent directly.

```bash
cd ~/workshop/sarasota_dem
ivert database download --date-start 2023.10.01 --date-end 2024.11.01 *_final.tif
```

| Option | Meaning |
|---|---|
| `--date-start 2023.10.01` | Start date for the ICESat-2 search |
| `--date-end 2024.11.01` | End date for the search |
| `*_final.tif` | The DEM(s) whose combined extent defines the download region |

This sends a request to the **NASA Harmony** cloud service, which subsets the granules *before* downloading them, saving many GB of transfer. IVERT then downloads the ancillary products needed to classify the photons.

This usually takes about **20 minutes**, and more than an hour for larger queries. Grab a coffee.

:::{dropdown} How much data should I request?

Request **at least a year**. ICESat-2 repeats its orbits every 90 days and cannot collect through clouds, so a shorter window leaves large parts of your DEM unsampled.

**Why the November 2024 end date?** ICESat-2's **ATL24** bathymetry product is only processed periodically, and as of **August 2026** it runs through **November 7, 2024**. NASA will process newer data in fall 2026, but for now request photons on or before November 2024 for the best chance of bathymetry classifications in a coastal area. IVERT knows this cutoff — the `atl24_date_cutoff` setting — and warns you if your range extends past it.

If your DEM is not near a coastline, none of this matters. ICESat-2 began collecting on October 13, 2018 and is still going in August 2026.
:::

:::{dropdown} Prefer a bounding box instead of a DEM?

`ivert database download` also accepts a 4-value bounding box in **W/E/S/N** order:

```bash
ivert database download --date-start 2023.10.01 --date-end 2024.11.01 -- -82.7/-82.5/27.3/27.5
```

The `--` delimiter explicitly ends the command-line options, which is required whenever your coordinates begin with a negative `-`. Add `--wsen` to give the box in W/S/E/N order instead.
:::

---

## 🔧 4. Check your database

```bash
ivert database list
```

You should see one row per granule:

```text
File                                                                   Total    Ground    BathyFloor    BathySurf
--------------------------------------------------------------------  -------  --------  ------------  -----------
ATL03_20231028200109_06082106_007_01_subsetted_..._20231001_20241101.nc  52,160       197            19            0
ATL03_20231124062932_10122102_007_01_subsetted_..._20231001_20241101.nc  24,434         0             3            6
ATL03_20240223020859_10122202_007_01_subsetted_..._20231001_20241101.nc  12,512         0             0            1
```

| Column | Meaning |
|---|---|
| `Total` | All photons retained in that granule |
| `Ground` | Ground returns (ATL08) — these validate the **terrestrial** part of your DEM |
| `BathyFloor` | Seafloor photons (ATL24) — these validate the **submerged** part |
| `BathySurf` | Water-surface photons |

:::{important} 🧪 Success check
:icon: false

There are files in the database, and they contain classified **ground** photons. If your DEM is coastal, check whether any have **bathymetry** photons (`BathyFloor`).

An empty list means the download found no overlapping granules or did not complete — re-check your date range and DEM extent. `BathyFloor` counts of `0` are not necessarily an error; clear, shallow water is a prerequisite for ICESat-2 bathymetry and many coastlines lack it.
:::

:::{dropdown} Reading the granule filenames

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

`ivert database size` reports how much disk space the database is using.
:::

---

## 🔧 5. Clear the download cache

IVERT pulls **raw** granules and ancillary products into a cache, then writes the classified photons out to the database. That cache is scratch space and is often **100x larger than the database it produced**.

```bash
ivert cache list
ivert cache delete
```

:::{important} 🧪 Success check
:icon: false

`ivert cache list` reports a `TOTAL` of 0 files, while `ivert database size` still reports all of your `.nc` granules. Your database is intact and you reclaimed the scratch space.
:::

:::{dropdown} What exactly am I deleting?

The `icesat2` subdirectory is nearly all of the disk space, holding raw granules — including noise photons — that have already been subsetted and classified into your database.

Only clear the cache **after** confirming your database looks right in step 4. Deleting it does not touch your database, but the cache also holds reusable ancillary files (`proj` conversion grids, the `bing` building mask, the `osm_landmask`), and IVERT will re-download or rebuild those if it needs them again.
:::

---

## 🔧 6. Validate against your own database

IVERT is still pointing at your new database, so validation works exactly as it did in Module 5:

```bash
ivert validate *_final.tif -n "Sarasota, FL" --overwrite
```

`--overwrite` tells IVERT to replace any previous results in the `ivert_results` sub-directory. The five output files are the same ones described in [Module 5](./index.md).

:::{dropdown} The takeaway

Three things to get right when building a database:

1. **Extent** — cover the DEM you actually want to validate
2. **Time span** — at least a year, and mind the ATL24 cutoff for coastal work
3. **Content** — confirm with `ivert database list` that you have classified photons, not just granules

Once built, the database is reusable: any number of DEMs overlapping that region can be validated without downloading anything again. Later requests only download areas and time spans you haven't already asked for, and IVERT subsets new areas that partially overlap old ones, so you get a full record without duplicate granules.
:::

---

## Where to go next

- Return to **[5 - Evaluate the DEMs with IVERT](./index.md)** for the validation workflow and how to interpret the outputs.
- `ivert classes` lists the photon classification codes, selectable with `-c/--classes` at download time.
- `ivert database export` writes your photons out to GeoPackage, Shapefile, or XYZ.
- Every command has detailed help: `ivert <command> --help`.
