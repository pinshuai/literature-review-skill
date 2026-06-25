# Search Log

## Round 1 — 2026-06-23

**Tool**: WebSearch + WebFetch (native Claude Code tools, substituted for `parallel-cli`,
which is not installed in this environment). DOI/metadata for every retained candidate was
independently confirmed against the CrossRef REST API (`api.crossref.org/works`), because
most publisher landing pages (Wiley, ScienceDirect, IOP) returned HTTP 402/403 to WebFetch.
**Date range targeted**: no hard cutoff; prioritized 2015–2026, with select foundational
paired-catchment studies (Scott 1993) retained for mechanistic grounding.

### Queries run (each run academic-domain-filtered and/or general)

1. `wildfire impact streamflow water yield forested catchment paired watershed`
2. `post-wildfire peak flow flood response burned catchment magnitude increase`
3. `wildfire snowmelt timing snowpack streamflow seasonal shift mountain catchment`
4. `post-fire annual water yield evapotranspiration increase forest burn severity gradient`
5. `post-wildfire streamflow recovery timescale vegetation regrowth western United States ecoregion`
6. `hydrologic modeling wildfire effects streamflow uncertainty distributed model burned watershed`
7. `wildfire flood frequency peak discharge increase western US gauged basins`
8. `fire effects runoff Mediterranean catchment streamflow Portugal Spain experimental`
9. `wildfire low flow summer streamflow change burned watershed quantile analysis`
10. `wildfire effect streamflow snow zone forested headwaters Colorado variability hydrologic response`
11. `large fire streamflow trends global rivers burned area annual runoff change`
12. `CONUS wildfire evapotranspiration spatial temporal response controlling factors`
13. `wildfire snow cover loss burned forest warmer climate western US energy balance albedo`
14. `post-fire watershed response burn severity precipitation regime integrated hydrologic model`
15. `post-wildfire runoff generation review synthesis Moody Martin Ebel watershed hydrology`
16. `Vulnerability of snowpack to wildfire changing climate western US ecoregions Hydrological Processes`
17. `wildfire streamflow paired catchment South Africa Australia Scott water yield afforestation`

### Candidate sources retained after screening (Round 1)

DOIs confirmed via CrossRef. Disposition: retained = met inclusion criteria (§2.2 of review);
excluded categories logged in Notes below.

| Key | Short title | DOI | Theme |
|---|---|---|---|
| scott1993 | Hydrological effects of fire in South African mountain catchments | 10.1016/0022-1694(93)90119-t | water yield / paired catchment |
| scott1990 | Wildfire effects on soil wettability, afforested catchment | 10.1016/0022-1694(90)90234-o | runoff / peak flow |
| saxe2018 | Controls on post-fire streamflow response, western US | 10.5194/hess-22-1221-2018 | water yield / controls |
| havel2018 | Assessing hydrologic response to wildfires in mountainous regions | 10.5194/hess-22-2527-2018 | water yield / modeling |
| hallema2018 | Burned forests impact water supplies | 10.1038/s41467-018-03735-6 | water yield / regional |
| wine2018a | Streamflow increases during post-wildfire recovery, western USA | 10.1088/1748-9326/aa9c5a | recovery |
| williams2022 | Growing impact of wildfire on western US water supply | 10.1073/pnas.2114069119 | water yield / regional |
| biederman2022 | Streamflow response differs with season and elevation, Lower Colorado | 10.1029/2021WR030687 | seasonal timing |
| wells2024 | Streamflow changes attributable to wildfire, semi-distributed model | 10.1002/eco.2697 | modeling |
| brown2025 | Universal hydrological trends obscured by local variability | 10.1088/1748-9326/ae114d | water yield / variability |
| wampler2023 | Modeling wildfire effects on streamflow, Cascade Mountains Oregon | 10.1016/j.jhydrol.2023.129585 | modeling |
| long2023 | Spatial analysis of streamflow trends in burned watersheds, western US | 10.1002/hyp.14949 | water yield / trends |
| miller2025 | Variability in hydrologic response between snow zones | 10.1002/hyp.70151 | seasonal / snow |
| collar2022 | Linking fire-induced ET shifts to streamflow magnitude and timing | 10.1016/j.jhydrol.2022.128242 | ET / timing |
| collar2021 | CONUS-scale wildfire and evapotranspiration | 10.1016/j.jhydrol.2021.127162 | ET |
| ma2020 | Wildfire controls on ET in California's Sierra Nevada | 10.1016/j.jhydrol.2020.125364 | ET |
| li2026 | Post-fire watershed response to burn severity and precipitation regimes | 10.1016/j.jhydrol.2025.134538 | modeling / peak flow |
| wagenbrenner2021 | Post-wildfire hydrologic recovery in Mediterranean climates (review) | 10.1016/j.jhydrol.2021.126772 | recovery / review |
| kang2024 | Hydrologic responses to wildfires in western Oregon | 10.1016/j.jhydrol.2024.131612 | water yield / peak flow |
| moreno2020 | Satellite-derived burn severity and streamflow at basin scale | 10.1016/j.jhydrol.2019.124244 | burn severity / streamflow |
| collar2025 | Rainfall as a driver of post-wildfire flooding and debris flows (review) | 10.1016/j.earscirev.2024.104990 | peak flow / review |
| guo2021 | Impacts of wildfire and climate variability on streamflow, Victoria AU | 10.1002/hyp.14439 | water yield / regional |
| koshkin2025 | Snow cover loss in burned forests, current and warmer climate | 10.1126/sciadv.adt9866 | snow / energy balance |
| koshkin2022 | Wildfire impacts on western US snowpacks (review) | 10.3389/frwa.2022.971271 | snow / review |
| giovando2022 | Wildfire impacts on snowpack phenology in a changing climate | 10.1029/2021wr031569 | snow timing |
| giovando2026 | Vulnerability of snowpack to wildfire and changing climate (ecoregions) | 10.1002/hyp.70377 | snow / climate |
| dilshan2025 | Wildfire-induced enhancement in downstream flood discharge, California | 10.1111/jfr3.70054 | peak flow / flood |
| pirani2025 | Survey of wildfire effects on peak flow characteristics (review) | 10.1007/s11269-025-04207-5 | peak flow / review |
| ebel2025 | Review and synthesis of post-wildfire shifts in hydrologic processes | 10.1088/3033-4942/ae2a64 | review / mechanisms |
| ebel2023 | Modeling post-wildfire hydrologic response (review) | 10.1029/2022EF003038 | modeling / review |
| beyene2021 | Parsing weather variability and wildfire effects on daily streamflows | 10.1029/2020wr028029 | quantile / low + peak flow |
| ebelfloods2024 | Upper limits for post-wildfire floods, distinction from debris flows | 10.1126/sciadv.adk5713 | peak flow |
| grillakis2025 | Diverse wildfire impacts on river flows across the globe | 10.1038/s43247-025-02419-6 | water yield / global |
| kampf2022 | Increasing wildfire impacts on snowpack in the western U.S. | 10.1073/pnas.2200333119 | snow timing |
| gleason2019 | Four-fold increase in solar forcing on snow in burned forests | 10.1038/s41467-019-09935-y | snow / energy balance |
| goeking2020 | Forests and water yield: synthesis of disturbance effects (review) | 10.1093/jofore/fvz069 | water yield / review |
| nolan2015 | ET and streamflow trends after wildfire, resprouting eucalypts | 10.1016/j.jhydrol.2015.02.045 | ET / regional |
| poon2018 | Spatial-temporal ET trends after wildfire, semi-arid landscapes | 10.1016/j.jhydrol.2018.02.023 | ET |
| khaledi2022 | Wildfire contribution to streamflow variability, Australian temperate zone | 10.1016/j.jhydrol.2022.127728 | water yield / regional |

### Notes

- ~60 candidate titles surfaced across 17 queries; 39 retained after title/abstract screening (including foundational snowpack, ET, flood, and review papers surfaced by the snow/ET/flood/synthesis queries).
- Excluded categories: water-quality/sediment-only studies (e.g., Portuguese 2017-fire water-quality,
  Colorado dynamic water-quality), debris-flow-hazard-only studies without a streamflow component,
  afforestation/forest-management studies without a fire treatment, conference/GSA abstracts and SSRN
  preprints (e.g., the SSRN preprint of Dilshan 2025 — the published JFRM version was retained instead),
  and purely groundwater/baseflow-recession-focused studies (out of scope for this review's
  streamflow/water-yield/peak-flow focus per the exclusion criteria in §2.2).
- Wiley/ScienceDirect/IOP landing pages returned HTTP 402/403 to WebFetch; authors/year/journal/DOI
  were therefore confirmed via the CrossRef REST API rather than full-text extraction.
- Round 1 total: 39 retained, citation-verified sources.

---

## Round 2 — 2026-06-23

**Tool**: `paper-search` CLI (`uv run --directory /Users/a02388352/paper-search-mcp paper-search search ...`),
querying CrossRef, OpenAlex, and Semantic Scholar (`-s crossref,openalex,semantic`).
**Goal**: extend Round 1 coverage past the 30-50 retained-source target with sub-topics and regions
(peak/flood flow, Australian and Canadian case studies, experimental-fire catchments, hydrologic-recovery
frameworks) under-represented in Round 1, using the same inclusion/exclusion criteria (§2.2 of review).

### Queries run (11 queries, 12 results per source)

1. `post-fire peak flow flood magnitude forested watershed`
2. `wildfire annual water yield increase paired catchment streamflow`
3. `post-wildfire snowmelt timing streamflow seasonal mountain`
4. `wildfire flood frequency analysis burned basin peak discharge`
5. `post-fire low flow summer streamflow change burned catchment`
6. `wildfire hydrological recovery vegetation regrowth streamflow timescale`
7. `SWAT model wildfire streamflow simulation burn severity watershed`
8. `wildfire climate change water supply mountain streamflow projection`
9. `post-fire stormflow runoff response rainfall intensity hillslope catchment`
10. `fuel treatment prescribed fire forest thinning water yield streamflow`
11. `bushfire streamflow Australia eucalypt forest water yield decline`

### Screening summary

- 425 raw candidate rows per query file; after merging all 11 queries and deduplicating by DOI,
  289 unique candidates with DOIs remained (after removing the 31 Round 1 DOIs).
- Keyword screening (must mention fire-term AND streamflow/water-yield/runoff/peak-flow/snow term;
  excluding debris-flow-only, water-quality/sediment/nutrient-only, dam/reservoir engineering,
  flood-plain, and design-flood titles) reduced this to 33 on-topic candidates.
- Of those, EGU/HESSD/Authorea/SSRN preprint duplicates of published papers were dropped in favor of the
  published version (e.g., `hess-2016-533` → published `hess-22-1221-2018`; `au.159294171...` →
  published `hyp.14439`; `egusphere-egu...` abstracts excluded). One chronic slash-and-burn study
  (Madagascar, `hyp.14937`) was excluded as not a wildfire study.
- 16 new candidates met inclusion criteria and were retained; all 16 DOIs verified via CrossRef
  (16/16 resolved with matching metadata), none duplicating a Round 1 source.

### New sources retained (Round 2)

| Key | Short title | DOI | Theme |
|---|---|---|---|
| kuczera1987 | Prediction of water yield reductions following a bushfire, ash eucalypt | 10.1016/0022-1694(87)90054-0 | water yield / regional |
| brookhouse2013 | Impact of bushfires on water yield, SE Australia ash forests | 10.1002/wrcr.20351 | water yield / regional |
| heath2014 | Post-wildfire recovery of water yield, Sydney Basin supply catchments | 10.1016/j.jhydrol.2014.09.033 | recovery / water yield |
| stoof2012 | Hydrological response of a small catchment burned by experimental fire | 10.5194/hess-16-267-2012 | runoff / experimental |
| seibert2010 | Wildfire effects on snow-dominated catchment runoff (modelling) | 10.2166/nh.2010.036 | snow / modeling |
| bart2016 | A regional estimate of postfire streamflow change in California | 10.1002/2014wr016553 | water yield / regional |
| wine2018b | Nonlinear long-term large-watershed hydrologic response to wildfire | 10.1029/2018ef000930 | water yield / trends |
| rengers2016 | Model simulations of flood and debris flow timing in steep catchments | 10.1002/2015wr018176 | peak flow / modeling |
| brogan2017 | Reconstructing extreme post-wildfire floods (convective vs mesoscale) | 10.1002/esp.4194 | peak flow |
| spencer2024 | Changes in snow-dominated streamflow quantity and timing, British Columbia | 10.1002/hyp.15278 | seasonal / snow |
| long2022 | Event-scale analysis of streamflow response to wildfire in Oregon 2020 | 10.3390/hydrology9090157 | peak flow / stormflow |
| meili2023 | Aerodynamic effects: higher ET and water-yield reductions after wildfire | 10.1111/gcb.16995 | ET / water yield |
| acharya2025 | Post-fire streamflow prediction from Landsat and UAV remote sensing | 10.3390/rs17223690 | modeling / streamflow |
| ebelrecovery2022 | Hydrologic recovery after wildfire: a framework of approaches/metrics | 10.2478/johh-2022-0033 | recovery / review |
| newcomer2023 | Prolonged drought suppresses wildfire impacts on hydrology, N. California | 10.1029/2022wr034206 | water yield / controls |
| zacharakis2026 | Post-wildfire recovery of vegetation, soil, hydrological responses (review) | 10.1016/j.earscirev.2026.105452 | recovery / review |

### Notes

- `paper-search` surfaced Australian (Kuczera 1987; Brookhouse 2013; Heath 2014), Canadian (Spencer &
  Winkler 2024, British Columbia), and European experimental-fire (Stoof 2012, Portugal) case studies
  largely absent from Round 1's western-US-dominated WebSearch results, broadening geographic coverage.
- A subset of retained Round 2 sources (seibert2010, bart2016, wine2018b, newcomer2023, zacharakis2026)
  also report on baseflow/recession alongside streamflow; they are retained here because they directly
  address streamflow/water-yield/peak-flow response (this review's scope), not only baseflow.

### Combined corpus

- **Round 1 + Round 2 total: 55 unique, citation-verified sources** (39 + 16; zero DOI duplicates).
- All 55 DOIs independently verified against the CrossRef REST API prior to inclusion.
