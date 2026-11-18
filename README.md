# ReproducibleYostIndex-data

Pre-computed Yost Index data files for the [ReproducibleYostIndex](https://github.com/HanLabCollaboration/ReproducibleYostIndex) R package.

The files are published as assets of the [latest release](https://github.com/HanLabCollaboration/ReproducibleYostIndex-data/releases/latest).

## Usage

`getYostIndex()` in ReproducibleYostIndex downloads these files automatically and caches them, so you don't need to download them manually:

```r
# install.packages("pak")
pak::pak("HanLabCollaboration/ReproducibleYostIndex")

library(ReproducibleYostIndex)
yost <- getYostIndex(geo = "tract", year = 2022, scope = "state", states = "CA")
yost_sf <- getYostIndex(geo = "county", year = 2024, geometry = TRUE)  # with boundaries
```

## Files

### CSV (gzip-compressed): one file per `(geo x scope x year)`

| geo | scope | years |
|---|---|---|
| county | national, state | 2011–2024 |
| tract | national, state | 2011–2024 |
| block group | national, state | 2013–2024 |

**80 files**, named `yost_{geo}_{scope}_{year}.csv.gz` (e.g. `yost_block_group_state_2020.csv.gz`).

- **national** scope: each geography is ranked against all US geographies of the same level.
- **state** scope: each geography is ranked within its state.

### GeoPackage: one file per `(geo x year)`

| geo | years |
|---|---|
| county | 2011–2024 |
| tract | 2011–2024 |
| block group | 2013–2024 |

**40 files**, named `yost_{geo}_{year}.gpkg`. Each has one layer, `geometry`, with `GEOID`, `NAME` and the boundary polygon for that year. The boundaries are the same for both scopes, so join them to any CSV on `GEOID`.

## Columns (CSV)

| Column | Description |
|---|---|
| `GEOID` | Census FIPS code: 5 digits (county), 11 (tract) or 12 (block group) |
| `NAME` | Census name of the geography |
| `year` | End year of the ACS 5-year estimates (e.g. 2022 = 2018–2022) |
| `geo` | `county`, `tract` or `block group` |
| `scope` | `national` or `state` |
| `Yost`, `YostQuintile` | Yost Index without stabilization or imputation |
| `YostStabilized`, `YostStabilizedQuintile` | Component variables stabilized with empirical Bayes before the factor analysis |
| `YostImputed`, `YostImputedQuintile` | Missing component values filled by spatial (queen contiguity) imputation |
| `YostStabilizedImputed`, `YostStabilizedImputedQuintile` | Both stabilized and imputed |

Quintiles are 1 (lowest SES) to 5 (highest), computed within the scope. Missing values mean the index could not be computed for that geography (e.g. no population or too many missing components).

## Reproducing the data

Every file was produced with `computeYostIndex()` from ReproducibleYostIndex. The scripts that regenerate a full release (a SLURM array job over all combinations) are in [`batch/hpc/`](https://github.com/HanLabCollaboration/ReproducibleYostIndex/tree/main/batch/hpc) of the package repository. Each release's notes record the package commit that was used.

## Sources

- Socioeconomic variables: US Census Bureau, American Community Survey 5-year estimates, retrieved via [tidycensus](https://walker-data.com/tidycensus/).
- Boundaries: US Census Bureau TIGER/Line shapefiles, retrieved via [tigris](https://github.com/walkerke/tigris).

This product uses Census Bureau data but is not endorsed or certified by the Census Bureau.
