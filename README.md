# Legacy Carbon Collaborative (LCC): data and code

Data and R code for:

> Walker, X. J. et al. (in review). 

**Archived release (cite this):** [DOI — add after release]
**Contact:** Xanthe J. Walker, xanthe.walker@nau.edu

---

## Overview

The Legacy Carbon Collaborative compiled radiocarbon-dated soil organic layer (SOL) profiles from burned boreal and tundra sites across eight circumpolar regions. For each profile we estimated how much prefire, residual, and combusted SOL carbon was **legacy carbon**: carbon that accumulated before the previous fire and persisted through it.

This repository contains:

- the **pipeline**, with its input files, that builds the profile-level dataset from site, radiocarbon, and soil C/N data
- the **archived dataset** and data dictionary that the pipeline produces
- the **analysis**, which reproduces every statistic, figure, and table in the manuscript from the archived dataset

**If you only want the data or the manuscript results**, use `data/LCC_LegacyCarbon_archive.csv` and run `code/02_LCC_analysis.Rmd`. You don't need to run the pipeline.

## Repository contents

```
.
├── README.md
├── LICENSE
├── code/
│   ├── 01_LCC_pipeline.Rmd   # radiocarbon ages, C pools, legacy C -> archived dataset + dictionary
│   └── 02_LCC_analysis.Rmd   # all manuscript statistics, figures, and tables
├── data/
│   ├── LCC_LegacyCarbon_archive.csv                    # archived profile-level dataset (pipeline output; analysis input)
│   ├── LCC_LegacyCarbon_archive_data_dictionary.csv    # column definitions, units, and groups
│   └── pipeline_inputs/
│       ├── LCC_site_input.csv                          # site-level data, one row per soil profile
│       ├── LCC_14C_long_clean.csv             # radiocarbon measurements, one row per sample
│       └── LCC_Soil_CN_clean.csv                       # depth-wise soil C, N, and bulk density
└── outputs/                                            # created when the code is run (not tracked)
```

## Data

### `LCC_LegacyCarbon_archive.csv`

- **258 soil profiles × 91 columns.** One row per radiocarbon-dated soil profile (`soilID`).
- `included_in_analysis = TRUE` marks the [224] profiles used in the manuscript. The other profiles are kept for completeness, and `exclusion_reason` says why each was excluded.
- Eight profiles contributed by Parisien from unburned stands, or from fires more than 20 years before sampling, are archived but not used in the manuscript.
- Column groups:
  - Identification
  - Site characteristics, including location, climate, stand age, forest type, ecosystem class (`Eco`), and moisture class
  - SOL properties
  - Radiocarbon: F14C, Δ14C, and assigned calendar year for the TOP, MID, and BOTTOM samples
  - Prefire, residual, and combusted SOL C, each split into legacy and new C, with the years each represents
  - Quality-control flags
- Coordinates are decimal degrees (WGS84). Missing values are `NA`. Zeros are true zeros: for example, a profile that had legacy C before the fire but lost none of it has `comb_legacy_C = 0`.

### `LCC_LegacyCarbon_archive_data_dictionary.csv`

Gives the group, units, and description for every column in the archive.

### Pipeline inputs

- **`LCC_site_input.csv`**: one row per soil profile. It contains site metadata harmonized across the contributing groups, climate (MAT, MAP, tree cover), measured SOL C pools and combustion where available, and the ForestGroup, moisture, and age classifications.
- **`LCC_14C_long_clean_08262025.csv`**: individual radiocarbon measurements (F14C, Δ14C, depth, and section) for each profile.
- **`LCC_Soil_CN_clean.csv`**: depth-wise soil carbon, nitrogen, and bulk density for each profile.

## Code

### `01_LCC_pipeline.Rmd`: building the archived dataset

The pipeline:

1. calibrates pre-bomb radiocarbon ages (Bchron, IntCal20)
2. assigns post-bomb ages from the atmospheric bomb curve (Hua et al. 2021, Northern Hemisphere Zone 1)
3. applies stratigraphic-order rules to each profile
4. combines ages with soil C/N data to calculate prefire, residual, and combusted SOL C pools
5. partitions each pool into legacy and new C
6. writes the archive and data dictionary


### `02_LCC_analysis.Rmd`: reproducing the manuscript results

This file reads only `data/LCC_LegacyCarbon_archive.csv`.

| Section | Manuscript item |
|---|---|
| Prevalence of legacy C | In-text means and proportions |
| Figure 1 | Stacked bar plots of SOL C pools by ecosystem |
| Ecosystem-level models | Figure 2 and Extended Data Table 2 statistics |
| Figure 2 | Occurrence and combustion probabilities by ecosystem |
| Figure 3 | Fire-free interval vs. SOL C interval; combusted vs. interval SOL C pool |
| Drivers models | Figure 4 statistics; supplementary model-selection table |
| Figure 4 | Fitted relationships |
| Extended Data Figs. 2–3 | Slopegraph; depth of the legacy C boundary |
| Extended Data Tables 1–3 | Site characteristics; pools and intervals; model coefficients |
| Supplementary Figs. 1–6 | Prefire and combustion characteristics, combustion calibration model, radiocarbon vs. calendar year, age-threshold sensitivity |

Models are fit with `glmmTMB`. Residual diagnostics (DHARMa) appear in tabs in the knitted HTML.

## How to run

Clone or download the repository and open it as an RStudio project, so that paths resolve from the repository root.

**Manuscript results only:** knit `code/02_LCC_analysis.Rmd`. Figures go to `outputs/figures/` and Word tables to `outputs/tables/`. Set `save_outputs = FALSE` in the YAML header to knit without writing files.

**Full rebuild:** knit `01_LCC_pipeline.Rmd`, then `02_LCC_analysis.Rmd`. The archive written by `01` should match the one in `data/`.

## Requirements


```r
install.packages(c(
  # both scripts
  "dplyr", "tidyr", "purrr", "stringr", "readr", "tibble", "ggplot2", "patchwork",
  "glmmTMB", "DHARMa", "conflicted", "Bchron", "SoilR",
  # analysis
  "emmeans", "multcomp", "multcompView", "ggeffects", "performance",
  "scales", "cowplot", "ggh4x", "knitr", "kableExtra", "flextable", "officer",
  # pipeline
  "forcats", "reshape2", "rcarbon", "lme4", "MuMIn", "glue"
))
```


## Citation

If you use these data or code, please cite the paper and the archived release:

> Walker, X. J. et al. ([year]). 
> Walker, X. J. et al. ([year]). Data and code for "[Title]". [DOI]

