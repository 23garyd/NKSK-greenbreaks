# NKSK Green Fuel Breaks — siting and cost pipeline

The Python pipeline behind the **[NKSK Green Fuel Break planning tool](https://23garyd.github.io/nksk-fuelbreak-tool/)**.
It scores candidate road segments in North Kona and South Kohala, on leeward Hawai'i Island, for
green fuel break treatment against combined ecological and economic criteria, and writes the scored
shapefiles, cost tables, and raster overlays that the browser tool serves.

Research conducted as an ORISE fellow with the USDA Forest Service, Institute for Pacific Islands
Forestry. First author on a manuscript in preparation.

## What it produces

- **280 candidate road segments** across 10 highways — 198 km (123 mi) of corridor and 1,188 ha
  (~2,935 acres) of treatment area
- **Per-segment drought probabilities** derived from 2000–2023 Hawai'i Climate Data Portal
  wet-season rainfall, rather than one district-wide constant
- **Native species suitability** for 20 species, modeled from NRCS climatic–elevation envelopes,
  climatic water deficit, elevation, and potential/actual evapotranspiration rasters — scored into
  low, medium and high palettes by elevation class
- **Three-year implementation and maintenance cost models** at a 2% discount rate: 87.0M / 93.3M /
  98.0M USD network-wide across the three palettes, covering 2.17M plants
- Young lava substrate screened out of candidate segments

## Stack

Python · GeoPandas · Rasterio · Shapely · PyProj · pandas · NumPy · Matplotlib · JupyterLab

---

# Running GreenFuelBreak_Pipeline.ipynb (miniconda + JupyterLab)

## 0. What you need
Your project folder (`26X_GFB_Data/`) should contain:

```
26X_GFB_Data/
├── GreenFuelBreak_Pipeline.ipynb   <- the notebook you run
├── environment.yml                 <- conda environment spec (provided)
├── inputs/                         <- all input data (road_segments, NKSK-boundaries,
│                                       plant-shapefiles, hawaii_dem, Penman/AET rasters, xlsx)
└── notebooks/                      <- the 5 original notebooks (not needed to run)
```

The notebook auto-detects `inputs/` when you launch Jupyter **from this folder**.

---

## 1. Open a terminal and confirm conda works
```bash
conda --version
```
If "command not found", open the **Anaconda Prompt** (Windows) or run
`source ~/miniconda3/bin/activate` (macOS/Linux) first.

## 2. Create the environment (one time)
From inside the project folder:
```bash
conda env create -f environment.yml
```
This makes a conda environment named **gfb** with the full geospatial stack
(GDAL/PROJ come bundled via conda-forge, so there is nothing else to compile).
It takes a few minutes.

> Prefer to build it by hand instead of the file? This is the equivalent:
> ```bash
> conda create -n gfb -c conda-forge python=3.11 geopandas rasterio shapely pyproj \
>   contextily mapclassify pandas numpy matplotlib openpyxl requests beautifulsoup4 \
>   jupyterlab ipykernel
> ```

## 3. Activate it
```bash
conda activate gfb
```
Your prompt should now start with `(gfb)`.

## 4. Register the environment as a Jupyter kernel (one time)
```bash
python -m ipykernel install --user --name gfb --display-name "Python (gfb)"
```

## 5. Launch JupyterLab from the project folder
```bash
jupyter lab
```

## 6. Open the notebook and pick the kernel
1. In the file browser, open **GreenFuelBreak_Pipeline.ipynb**.
2. Top-right kernel selector -> choose **Python (gfb)**.

## 7. Run it
- Run everything: menu **Run -> Run All Cells**, or run cell by cell with **Shift+Enter**.
- Step 0 prints an "Input check" table. Every row should say `OK`. If any says
  `MISSING`, fix the path (see Troubleshooting) before continuing.

Generated files (scored shapefile, CWD rasters/tables, watering CSV, cost CSV, and all maps)
are written to a new **`gfb_outputs/`** folder inside the project folder.

---

## Good to know
- **Step 1 (species scraper) is optional and normally skips itself.** It only runs
  if `inputs/gonative_combined_scored_3_13.xlsx` is missing. You do not need internet
  for the rest of the pipeline.
- **Map basemaps need internet.** The Esri shaded-relief topo layer is fetched online.
  With no connection the maps still render (border, roads, colors), just without the
  topographic backdrop.
- **The first code cell (0a) runs `pip install ...`.** Inside the `gfb` env everything
  is already present, so pip just reports "already satisfied" and changes nothing.
- **Re-running:** outputs overwrite cleanly. Safe to Run All again.

## Changing where inputs/outputs live
Open the **master config** cell (Step 0b) and set the paths directly, e.g.:
```python
INPUT_DIR  = "/path/to/26X_GFB_Data/inputs"
OUTPUT_DIR = "/path/to/26X_GFB_Data/gfb_outputs"
```

## Troubleshooting
- **`Input check` shows MISSING** — you launched Jupyter from a different folder. Either
  `cd` into `26X_GFB_Data` before `jupyter lab`, or hard-set `INPUT_DIR` in Step 0b.
- **Kernel "Python (gfb)" not listed** — re-run step 4, then refresh JupyterLab.
- **`conda env create` fails on solving** — update conda first: `conda update -n base conda`,
  then retry. Make sure you are on the `conda-forge` channel (the yml already sets this).
- **Want to start over** — `conda env remove -n gfb` and repeat from step 2.
