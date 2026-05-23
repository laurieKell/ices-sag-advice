# ICES SAG extraction (advice)

Extract ICES **Stock Assessment Graph (SAG)** time series and **Advice View** data for all published advice stocks, retained by **assessment year**.

## What it produces

Knitting `Rmd/getSAG.Rmd` builds `data/sag/sag_<yrFrom>_<yrTo>.RData` containing:

| Object | Description |
|--------|-------------|
| `sag_ts` | Calendar-year series: SSB, F, recruits, catch |
| `sag_refpts` | Reference points (Blim, FMSY, MSYBtrigger, …) per stock × assessment year |
| `sag_advice` | Catch advice from Advice View |
| `sag_stock` | One row per stock × assessment year (refpts + primary advice) |
| `sag_scenarios` | Catch scenario tables (if `fetch_scenarios: true`) |
| `stockIndex`, `grid`, `rawList` | Stock metadata and raw SAG summary tables |

## Requirements

- R ≥ 4.2
- Packages: **icesSAG**, **icesASD**, **plyr**, **dplyr**, **tidyr**, **knitr**, **rmarkdown**
- Optional (nicer HTML tables): **kableExtra**, **ggplot2** (example plot)

```r
install.packages(c("icesSAG", "icesASD", "plyr", "dplyr", "tidyr", "knitr", "rmarkdown"))
# icesSAG / icesASD may need:
# install.packages(c("icesSAG", "icesASD"), repos = c("https://ices-tools-prod.r-universe.dev", "https://cloud.r-project.org"))
```

Network access to `sag.ices.dk` and `asd.ices.dk` is required for a full extract.

## Quick start

```r
setwd("path/to/advice")   # project root (parent of Rmd/)
rmarkdown::render("Rmd/getSAG.Rmd")
load("data/sag/sag_2016_2025.RData")

subset(sag_ts, sid == "mac.27.nea" & assYr == 2025)
subset(sag_refpts, sid == "mac.27.nea" & assYr == 2025)
```

## Parameters

Edit the YAML header in `Rmd/getSAG.Rmd`:

| Parameter | Default | Meaning |
|-----------|---------|---------|
| `yrFrom`, `yrTo` | 2016, 2025 | Assessment years to query |
| `fetch_scenarios` | `false` | Download full catch scenario tables (slow) |
| `api_retries` | 5 | Retries on API failure |
| `api_wait_s` | 20 | Seconds between retries |
| `connect_timeout_s` | 60 | Connection timeout |
| `request_timeout_s` | 300 | Request timeout |
| `reuse_stock_list` | `true` | Fall back to saved stock list if API fails |

## Project layout

```
advice/
├── README.md
├── Rmd/
│   └── getSAG.Rmd      # main extraction report
└── data/
    └── sag/            # output .RData (gitignored; created by knit)
```

## Related work

Workflow based on patterns in the Resilience project (`getSAG`, `getAdviceViewRecord` in `report/` and `scripts/advice.R`).

## Licence

MIT — see [LICENSE](LICENSE).
