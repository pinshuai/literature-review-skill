# Search Log

## Round 1 — 2026-06-17

**Tool**: WebSearch (native Claude Code tool, substituted for parallel-cli which is not installed locally)
**Date range targeted**: no hard cutoff; prioritized 2015–2026 with key foundational citations included

## Queries run

1. "post-fire streamflow recession groundwater recharge Western US snowmelt watershed"
2. "wildfire effect snowpack accumulation melt timing forest canopy loss mountain hydrology"
3. "post-wildfire evapotranspiration changes water yield increase forested watershed"
4. "burn severity soil hydraulic properties infiltration subsurface flow paths wildfire"
5. "paired catchment study wildfire low flow baseflow recession constant long-term"
6. "wildfire hydrology review synthesis streamflow generation mechanisms snow-dominated"
7. "climate change increasing wildfire frequency severity compound effects mountain water supply future projections"
8. "vegetation recovery post-fire transpiration recovery time streamflow return to pre-fire baseline"

## Candidate sources identified (pre-screening)

- Ebel et al. (2026?) "A review and synthesis of post-wildfire shifts in hydrologic processes and streamflow generation mechanisms" — IOP Science / USGS — KEY REVIEW
- "Characterization and evaluation of controls on post-fire streamflow response across western U.S. watersheds" — USGS
- "Wildfire-induced shifts in groundwater discharge to streams identified with paired air and stream water temperature analyses" — USGS / J. Hydrol.
- "Growing impact of wildfire on western US water supply" — PNAS 2022
- "Increasing wildfire impacts on snowpack in the western U.S." — PNAS 2022
- "Impact of current and warmer climate conditions on snow cover loss in burned forests" — Science Advances
- "Wildfire and topography impacts on snow accumulation and retention in montane forests" — J. Hydrol. (ScienceDirect)
- "Increased water yield and altered water partitioning follow wildfire in a forested catchment in the western United States" — Blount et al. 2020, Ecohydrology
- "The impact of wildfire on baseflow recession rates in California" — J. Hydrol. (ResearchGate)
- "Wildfire impacts on groundwater recharge in mountain catchments" — Sci. Total Environ. (ScienceDirect)
- "Implications of fire-induced evapotranspiration shifts for recharge-runoff generation and vegetation conversion in the western United States" — USGS / J. Hydrol.
- "A novel Budyko-based approach to quantify post-forest-fire streamflow response and recovery timescales" — J. Hydrol.
- "In ecoregions across western USA streamflow increases during post-wildfire recovery" — Environ. Res. Lett. 2017
- "Post-wildfire recovery of forest vegetation, soil, and hydrological responses: A review" — Earth-Science Reviews 2026
- "Groundwater dominates snowmelt runoff and controls streamflow efficiency in the western United States" — Commun. Earth Environ. 2025
- "Modeling Post-Wildfire Hydrologic Response: Review and Future Directions for Applications of Physically Based Distributed Simulation" — Ebel & Mirus, Earth's Future 2023

## Notes

- ScienceDirect/Wiley results often abstract-only via WebFetch (paywalled); citation metadata (authors/year/DOI) confirmed via search snippets + publisher landing pages where possible.
- USGS Pubs Warehouse pages used as authoritative source for USGS-authored papers (open access).
- Round 1 yielded 14 retained sources after screening ~30 candidates.

---

## Round 2 — 2026-06-18

**Tool**: `paper-search` CLI (`uv run --directory /path/to/paper-search-mcp paper-search search ...`), querying CrossRef, OpenAlex, and Semantic Scholar (`-s crossref,openalex,semantic`)
**Goal**: systematically extend Round 1 coverage to >=30 unique retained papers total, using the same inclusion/exclusion criteria as Round 1 (see §2.2 of the review).

### Queries run

1. `post-fire baseflow recession streamflow wildfire` (q01_baseflow_recession)
2. `wildfire groundwater recharge forested catchment` (q02_groundwater_recharge)
3. `wildfire snowpack canopy loss snowmelt timing` (q03_snowpack_canopy)
4. `post-fire evapotranspiration water yield forest` (q04_et_water_yield)
5. `burn severity soil hydraulic properties infiltration` (q05_burn_severity_infiltration)
6. `paired catchment wildfire streamflow study` (q06_paired_catchment)
7. `wildfire hydrology review synthesis streamflow mechanisms` (q07_review_synthesis)
8. `climate change wildfire compound effects water supply mountain` (q08_climate_compound)
9. `vegetation recovery post-fire streamflow timescale` (q09_vegetation_recovery)
10. `snow-dominated catchment wildfire baseflow groundwater` (q10_snowdominated_baseflow)

Each query run with `-n 15` (15 results per source). Raw outputs saved per-query (`q01...q10*.json` in working directory) due to combined output size.

### Screening summary

- 236 raw candidate rows retrieved across all 10 queries and 3 sources.
- Deduplicated by DOI to 234 unique candidates.
- All 234 screened against the Round 1 inclusion/exclusion criteria (§2.2): peer-reviewed journal articles with direct treatment of post-fire streamflow/baseflow/low flow/groundwater recharge/snowpack/ET in forested or montane catchments; excluding peak-flow/flood/debris-flow/water-quality-only studies without a baseflow component, non-forested/urban WUI studies, preprints/non-peer-reviewed sources, and unverifiable paywalled sources.
- 34 candidates met inclusion criteria and were retained; 0 overlapped with the 14 Round 1 sources (no duplicates by DOI).
- All 34 new DOIs verified via `verify_citations.py` (34/34 verified, 0 failed) prior to merging into the main reference list.
- Combined Round 1 + Round 2 total: 48 unique, citation-verified sources.

### Notes

- `paper-search` returned several otherwise-hard-to-find Mediterranean (Spain, Portugal, Italy) and Australian case studies not surfaced by Round 1's WebSearch-based approach, broadening geographic coverage beyond the western US.
- A small number of candidates (conference abstracts, book chapters, and papers on fire ecology/water quality without a baseflow or recharge component) were excluded during screening; these are not individually logged here but were excluded per the criteria stated above.
