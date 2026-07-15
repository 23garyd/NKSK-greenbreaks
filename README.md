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

The notebook auto-detects `inputs/` when you launch Jupyter **from this folder**, and it
also has your `~/Desktop/26X_GFB_Data/inputs` path hard-coded as a fallback, so either way
it will find the data.

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
cd ~/Desktop/26X_GFB_Data
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
cd ~/Desktop/26X_GFB_Data
jupyter lab
```
This opens JupyterLab in your browser.

## 6. Open the notebook and pick the kernel
1. In the file browser, open **GreenFuelBreak_Pipeline.ipynb**.
2. Top-right kernel selector -> choose **Python (gfb)**.

## 7. Run it
- Run everything: menu **Run -> Run All Cells**, or run cell by cell with **Shift+Enter**.
- Step 0 prints an "Input check" table. Every row should say `OK`. If any says
  `MISSING`, fix the path (see Troubleshooting) before continuing.

That's it. Generated files (scored shapefile, CWD rasters/tables, watering CSV,
cost CSV, and all maps) are written to a new **`gfb_outputs/`** folder inside the
project folder.

---

## Good to know
- **Step 1 (species scraper) is optional and normally skips itself.** It only runs
  if `inputs/gonative_combined_scored_3_13.xlsx` is missing. You do not need internet
  for the rest of the pipeline.
- **Map basemaps need internet.** The Esri shaded-relief topo layer is fetched online.
  With no connection the maps still render (border, roads, colors), just without the
  topographic backdrop.
- **The first code cell (0a) runs `pip install ...`.** Inside the `gfb` env everything
  is already present, so pip just reports "already satisfied" and changes nothing. You
  can comment that cell out if you prefer.
- **Re-running:** outputs overwrite cleanly. Safe to Run All again.

## Changing where inputs/outputs live
Open the **master config** cell (Step 0b) and set the paths directly, e.g.:
```python
INPUT_DIR  = "/Users/garyding/Desktop/26X_GFB_Data/inputs"
OUTPUT_DIR = "/Users/garyding/Desktop/26X_GFB_Data/gfb_outputs"
```

## Troubleshooting
- **`Input check` shows MISSING** — you launched Jupyter from a different folder. Either
  `cd` into `26X_GFB_Data` before `jupyter lab`, or hard-set `INPUT_DIR` in Step 0b.
- **Kernel "Python (gfb)" not listed** — re-run step 4, then refresh JupyterLab.
- **`conda env create` fails on solving** — update conda first: `conda update -n base conda`,
  then retry. Make sure you are on the `conda-forge` channel (the yml already sets this).
- **Want to start over** — `conda env remove -n gfb` and repeat from step 2.
