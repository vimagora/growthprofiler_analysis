# GrowthProfiler analysis of yeast sugar-transporter strains

An R Markdown report that turns GrowthProfiler growth curves and end-point
yPet fluorescence into growth parameters, figures and tables for yeast strains
expressing heterologous sugar transporters (here: fungal sugar transporters,
anonymized as GOI01, GOI02, ..., in *S. cerevisiae* IMK1010 and IMK1010-MSM).

For every well it calculates, without a growth model:

| Parameter | How |
|---|---|
| µmax (1/h) | Steepest straight stretch of log(OD), `growthrates::fit_easylinear` (Hall *et al.* 2014) |
| Time to OD 1 (h) | First time point with smoothed OD ≥ 1 |
| Max OD | Highest smoothed OD |
| Deceleration start OD | OD where the growth rate falls below 50 % of µmax |
| AUC (OD x h) | Area under the min-subtracted curve |
| yPet / OD | End-point fluorescence / final OD (transporter expression) |

and reports growth calls per strain and condition, heat-map overviews, one
page per host strain × substrate, a per-well quality check and CSV tables.
The report explains each choice (e.g. why growth rates use only OD ≥ 1).

## Requirements

R (≥ 4.3) with:

```r
install.packages(c("tidyverse", "readxl", "growthrates", "patchwork",
                   "ggh4x", "ggpubr", "svglite", "knitr", "rmarkdown"))
```

## Data layout

Only the template and example files are in this repository; put your own
files next to them with the same naming.

```
raw_data/
├── growthprofiler_MD.tsv                    # your metadata (template: growthprofiler_MD_template.tsv)
├── growth_profiler/
│   └── <plate_id>_OD.csv                    # one GrowthProfiler OD export per plate (example: *_MTP0X_OD_example.csv)
└── yPET_fluorescence/
    └── <anything>_MTPxx.xlsx                # one Tecan Spark export per plate (example: *_MTP04_example.xlsx)
```

- **Metadata** (`growthprofiler_MD.tsv`, tab-separated): one row per well with
  `plate_id, row, column, well, host_strain, vector, goi, substrate,
  concentration`. Unused wells have `host_strain = empty`. Concentrations are
  text, e.g. `2.00%`.
- **GrowthProfiler OD files**: named `<plate_id>_OD.csv`, exactly as the
  `plate_id` in the metadata; 33 header lines, then `Time (min)` and one column
  per well.
- **Fluorescence files**: the plate code `MTPxx` at the end of the file name
  must match the end of the `plate_id`. The report reads the second reading
  (TF_GFP, ex 485 / em 530) from cells A88:M95; change `YPET_RANGE` if your
  export differs.

## Running

1. Open `growthprofiler.Rproj` in RStudio.
2. In `growthprofiler_report.Rmd`, edit the **Analysis parameters** chunk:
   `selected_strains`, `selected_conditions`, `POSITIVE_CONTROL`, and the
   thresholds if needed.
3. Knit. The report (`growthprofiler_report.html`), PDFs and SVGs go to
   `report_and_figures/`, the CSV tables to `tables/`.

## Repository contents

| Path | What |
|---|---|
| `growthprofiler_report.Rmd` | The analysis report |
| `raw_data/` | Metadata template and example input files |

## License

MIT, see [LICENSE](LICENSE).
