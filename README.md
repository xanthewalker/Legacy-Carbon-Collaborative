# Legacy-Carbon-Collaborative

# Legacy Carbon Collaborative (LCC): data and analysis code

**Contact:** Xanthe J. Walker, Center for Ecosystem Science and Society, Northern Arizona University (xanthe.walker@nau.edu)

---
# Overview

The Legacy Carbon Collaborative compiled radiocarbon-dated soil organic layer (SOL) profiles from burned boreal and tundra sites across eight circumpolar regions. For each profile we estimated how much prefire, residual, and combusted SOL carbon was **legacy carbon**, meaning carbon that accumulated before the previous fire and persisted through it.

This repository contains the full workflow: the input data from each contributing group, the pipeline that builds the profile-level dataset, the dataset itself, and the code that reproduces every statistic, figure, and table in the manuscript.

**If you only want the dataset**, use `data/LCC_LegacyCarbon_archive.csv` and its data dictionary. You don't need to run the pipeline.

## Repository contents

```
.
├── README.md
├── LICENSE
├── code/
│   ├── 01_LCC_build_site_input.Rmd   # harmonizes site data -> one row per soil profile
│   ├── 02_LCC_pipeline.Rmd           # radiocarbon ages, C pools, legacy C -> archive + dictionary
│   └── 03_LCC_analysis.Rmd           # all manuscript analyses, figures, and tables
├── data/
│   ├── LCC_LegacyCarbon_archive.csv                    # FINAL profile-level dataset (pipeline output)
│   ├── LCC_LegacyCarbon_archive_data_dictionary.csv    # column definitions, units, and groups
│   ├── raw/                                            # inputs contributed by each group
│   │   ├── LCC_Site_Data_08_26_2025.csv                # LCC collaborator site data
│   │   ├── NWT_Site_Data_Walker.csv                    # NWT site data (Walker/Mack)
│   │   ├── PLOT_03172025_final.csv                     # Alaska site/plot data (Walker)
│   │   ├── NWT_walker_SOL_C.csv                        # measured SOL C pools, NWT (Walker/Mack)
│   │   ├── LCC_newSOLcombustion.csv                    # field-measured SOL C combustion (Diaz-Veraverbeke, Gundale, Metcalfe)
│   │   ├── LCC_14C_long_clean_08262025.csv             # radiocarbon measurements, long format
│   │   ├── LCC_Soil_CN_clean.csv                       # depth-wise soil C, N, bulk density
│   │   ├── 2025-12-02_LCC_SpatialData.csv              # MAT, MAP, tree cover (collaborator sites)
│   │   ├── 2025-11-26_LC_site_SpatialData.csv          # MAT, MAP, tree cover (Walker Alaska sites)
│   │   └── D14C_age_new.csv                            # supplementary atmospheric D14C by year
│   └── intermediate/                                   # written by 01 and 02; rebuilt on each run
└── outputs/                                            # created when 03 is knit (not tracked)
    ├── figures/
    └── tables/
```

## Data

### `LCC_LegacyCarbon_archive.csv`

- **258 soil profiles × 92 columns.** One row per radiocarbon-dated soil profile (`soilID`).
- **`included_in_analysis`**: `TRUE` for the [224] profiles used in the manuscript. `FALSE` profiles are kept for completeness, and `exclusion_reason` says why each was excluded.
- Profiles contributed by Parisien from unburned stands, or from fires more than 20 years before sampling, are archived here but not used in the manuscript.
- Column groups:
  - Identification: researcher, study area, fire, site, plot, profile IDs
  - Site characteristics: location, climate, stand age, forest type, ecosystem class (`Eco`), moisture class
  - SOL properties: depths, bulk density, %C, %N
  - Radiocarbon: F14C, Δ14C, and assigned calendar year for the TOP, MID, and BOTTOM samples
  - Carbon pools and intervals: prefire, residual, and combusted SOL C, each split into legacy and new C, with the years each represents
  - Quality-control flags: age inversions, relabelled samples, imputed stand age, imputed burn depth
- **Coordinates** are decimal degrees (WGS84) at the precision supplied by each contributing group.
- **Missing values** are coded `NA`. Zeros are true zeros. For example, a profile that had legacy C before the fire but lost none of it has `comb_legacy_C = 0`.

### `LCC_LegacyCarbon_archive_data_dictionary.csv`

Gives the group, units, and a description for every column in the archive.

### How the archive was built

The archive was produced from site, radiocarbon, and soil C/N data by a processing pipeline that:

1. harmonizes site metadata
2. calibrates pre-bomb radiocarbon ages (Bchron, IntCal20)
3. assigns post-bomb ages from the atmospheric bomb curve (Hua et al. 2021, Northern Hemisphere Zone 1)
4. applies stratigraphic-order rules to each profile
5. partitions SOL C into legacy and new pools

The Methods and Supplementary Information describe these steps. The code for steps 1–5 is in `code/01_*` and `code/02_*`.

## Reproducing the analyses

### Requirements

```r
install.packages(c(
  "dplyr", "tidyr", "purrr", "stringr", "readr", "tibble",
  "glmmTMB", "DHARMa", "emmeans", "multcomp", "multcompView", "ggeffects", "performance",
  "ggplot2", "scales", "patchwork", "cowplot", "ggh4x",
  "knitr", "kableExtra", "flextable", "officer", "conflicted",
  "Bchron", "SoilR"   # reference curves only (IntCal20, Hua 2021), used in Supplementary Fig. 5
))
```

Running the pipeline (`01` and `02`) also requires `reshape2`, `forcats`, `rcarbon`, `lme4`, `MuMIn`, and `glue`.

### Run

To reproduce the dataset from the raw inputs, knit the files in order. To reproduce only the manuscript results, run `03` alone.

1. `01_LCC_build_site_input.Rmd` writes `data/intermediate/LCC_site_input.csv`.
2. `02_LCC_pipeline.Rmd` writes `data/LCC_LegacyCarbon_archive.csv` and the data dictionary.
3. `03_LCC_analysis.Rmd` writes figures and tables to `outputs/`.


Figures are written to `outputs/figures/` (PNG and PDF), and Word tables to `outputs/tables/`. To knit without writing any files, set `save_outputs = FALSE`.

