# Wildfire Impacts on Streamflow in Forested Catchments

**Date**: 2026-06-23
**Review Type**: Narrative Review
**PRISMA Compliance**: Partial (search and screening documented; not a formal systematic review/meta-analysis)

---

## Abstract

**Background**: Wildfire activity, extent, and severity have increased sharply across the western United States and many other forested regions over the past four decades. Because forested, often montane catchments supply a disproportionate share of regional water resources, understanding how fire alters streamflow — annual water yield, peak and flood flows, seasonal timing, and low flows — is a pressing applied and scientific problem.

**Objectives**: This review synthesizes current evidence on how wildfire affects streamflow in forested and montane catchments, addressing: (1) the direction, magnitude, and persistence of changes in annual water yield and the evapotranspiration (ET) mechanism that drives them; (2) the magnitude and controls of post-fire peak-flow and flood response; (3) wildfire-induced shifts in seasonal flow timing and low flows, particularly in snow-dominated systems; (4) how burn severity, climate, and vegetation recovery modulate these responses; and (5) the capabilities and uncertainties of hydrologic models used to simulate fire effects on streamflow.

**Methods**: Literature was identified in two rounds. Round 1 used Claude Code's native WebSearch/WebFetch tools (substituting for the `parallel-cli` tool specified in the literature-review skill, which was not installed) and yielded 39 sources. Round 2 used the dedicated `paper-search` CLI (querying CrossRef, OpenAlex, and Semantic Scholar) across eleven topic-targeted queries to broaden geographic and thematic coverage; 16 additional sources met inclusion criteria after screening 289 unique candidates. In total, 55 retained primary and review sources published 1987–2026 were selected, and every DOI was independently verified against the CrossRef API. This remains a narrative synthesis, not an exhaustive systematic review.

**Results**: Evidence converges on several robust patterns. Wildfire reliably increases annual water yield for several years post-fire, driven primarily by reduced ET following canopy and root loss; reported first-year increases range from a few percent in humid forests to well over 100% where a large fraction of a catchment burns under dry-to-average antecedent conditions. The response is strongly contingent on burn extent, burn severity, climate, and pre-fire vegetation, and at the largest scales the fire signal can be masked by watershed variability and concurrent weather. Peak and flood flows are the most dramatically affected: high-severity burns can raise flood peaks for a given storm by one to two orders of magnitude, mediated by reduced infiltration, loss of surface cover, and rainfall intensity thresholds. In snow-dominated catchments, canopy loss and albedo reduction advance and accelerate snowmelt, shifting flow earlier and altering seasonal partitioning, though the net effect on annual yield varies by elevation and snow zone. Recovery toward pre-fire behavior typically takes from a few years to more than a decade and is contingent on ecoregion, vegetation trajectory, and post-fire management.

**Conclusions**: The "wildfire increases streamflow" relationship is well established for annual yield and peak flows but is quantitatively heterogeneous, governed by an interacting set of controls (burn severity and extent, climate seasonality, vegetation recovery rate, and — in mountains — snow processes). The clearest gaps are the scarcity of long-term and large-basin observational records, weak constraint of snow-zone seasonal-timing effects, and persistent modeling uncertainty in representing fire-altered infiltration and ET.

**Registration**: Not registered (narrative review).
**Keywords**: wildfire, streamflow, water yield, peak flow, flood, snowmelt timing, evapotranspiration, burn severity, post-fire hydrology, forested catchments

---

## 1. Introduction

### 1.1 Background and Context

Forested mountain catchments are the dominant source of surface-water supply across the western United States and many temperate regions worldwide, and their streamflow is increasingly exposed to wildfire. The annual fraction of western US forest area burned in large wildfires grew by roughly 1,150% between 1984 and 2020, and among basins where more than 20% of forest burned, post-fire streamflow was elevated by an average of about 30% for roughly six years [@williams2022]. These trends are not confined to North America: regional analyses document material fire effects on streamflow in Australia's temperate and ash-eucalypt forests [@khaledi2022; @brookhouse2013; @guo2021] and, at the global scale, diverse and regionally contingent wildfire imprints on river flow [@grillakis2025].

Streamflow integrates several distinct responses to fire that do not necessarily move together. Annual water yield typically rises because the loss of canopy and roots reduces evapotranspiration; peak and flood flows can rise far more steeply because fire reduces infiltration and surface roughness; and in snow-dominated catchments the seasonal *timing* of flow shifts as canopy loss and snowpack darkening accelerate melt. Because these components respond to different mechanisms and on different timescales, "how wildfire affects streamflow" is best treated as a set of related but separable questions rather than a single quantity.

### 1.2 Scope and Objectives

This review focuses on streamflow in forested and montane catchments: annual/seasonal water yield, peak and flood flows, and flow timing including low flows. It includes empirical paired-catchment and gauge-record studies, regional multi-basin analyses, and hydrologic modeling studies. Studies focused purely on baseflow recession and groundwater recharge are addressed only where they bear directly on streamflow yield or timing; the narrower baseflow-mechanism question is outside this review's scope. Water-quality, sediment, and debris-flow-hazard literatures are excluded except where a study also reports a streamflow or flood-flow response.

**Primary Research Questions:**
1. What is the direction, magnitude, and persistence of wildfire's effect on annual water yield, and what mechanism drives it?
2. How large is the post-fire peak-flow and flood response, and what controls its magnitude?
3. How does wildfire shift seasonal flow timing and low flows, especially in snow-dominated catchments?
4. How do burn severity, climate, and vegetation recovery moderate these responses, and over what recovery timescales?
5. What can hydrologic models resolve about fire–streamflow response, and where do their uncertainties lie?

### 1.3 Significance

Water-supply planning, flood-hazard assessment, reservoir operation, and post-fire watershed rehabilitation all rest on assumptions about how fire changes streamflow. Recent synthesis work [@ebel2025] emphasizes that, while the direction of the annual-yield response is fairly well established, the magnitude and persistence of post-fire streamflow change — and its dependence on burn severity, climate, and recovery — remain incompletely constrained, particularly at large basin scales and in snow-dominated systems. Consolidating the current evidence clarifies what is established, what is contested, and where the highest-value observational and modeling gaps lie.

---

## 2. Methodology

### 2.1 Search Strategy

**Tooling note**: The literature-review skill's documented primary search tool, `parallel-cli`, was not available in this environment (not installed, and installation/authentication was not pursued). Claude Code's native `WebSearch` and `WebFetch` tools were substituted for Round 1, using academic-domain-targeted queries; the `paper-search` skill/CLI (CrossRef, OpenAlex, Semantic Scholar) was used directly for Round 2. This substitution is a deviation from the skill's default workflow and is disclosed here per the skill's transparency requirements. Because most publisher landing pages (Wiley, ScienceDirect, IOP) returned HTTP 402/403 to WebFetch, author/year/journal/DOI metadata for every retained source was independently confirmed against the CrossRef REST API rather than by full-text extraction.

**Round 1 — WebSearch/WebFetch** (17 queries, run 2026-06-23): queries spanned wildfire effects on streamflow/water yield in paired and forested catchments; peak-flow and flood response; snowmelt-timing and snowpack effects; post-fire ET and burn-severity gradients; recovery timescales by ecoregion; hydrologic modeling and uncertainty; flood-frequency and peak-discharge change in gauged basins; quantile analyses of low- and high-flow change; snow-zone variability in forested headwaters; global/large-basin runoff trends; and South African and Australian paired-catchment studies. The full query list and per-query disposition are in `sources/search_log.md`.

**Round 2 — `paper-search` CLI** (11 queries against CrossRef, OpenAlex, and Semantic Scholar, run 2026-06-23; up to 12 results per source per query): post-fire peak/flood flow; annual water-yield increase in paired catchments; snowmelt-timing and seasonal shifts; flood-frequency and peak discharge in burned basins; low/summer-flow change; hydrological recovery and vegetation regrowth; SWAT/distributed modeling of fire effects; climate-change/water-supply projections; stormflow and rainfall-intensity response; fuel-treatment and prescribed-fire effects on yield; and Australian bushfire/eucalypt water-yield studies. This round returned 289 unique candidate DOIs (after removing Round 1 entries); keyword and abstract screening retained 16 (see `sources/search_log.md`).

### 2.2 Inclusion and Exclusion Criteria

**Inclusion:**
- Peer-reviewed journal articles, plus AGU/PNAS/Nature-family and agency-affiliated journal articles
- Direct treatment of post-fire streamflow, water yield, peak/flood flow, seasonal flow timing, low flow, or the ET/snow processes that govern them, in forested or montane catchments
- Empirical (paired-catchment, long-term gauge, regional multi-basin) or hydrologic-modeling studies
- English language
- Priority to 2000–2026, with select foundational paired-catchment studies (Scott 1990/1993; Kuczera 1987) retained for mechanistic grounding

**Exclusion:**
- Studies focused exclusively on water quality (sediment, nutrients, contaminants) or on debris-flow hazard without a streamflow/flood-flow component
- Studies focused purely on baseflow recession or groundwater recharge with no streamflow-yield/peak-flow component (out of scope for this review)
- Non-forested or urban wildland–urban-interface fire studies without catchment-hydrology framing
- Preprints, conference abstracts (GSA, EGU, SSRN, ESSOAr/Authorea) — where a published version existed it was retained instead
- Sources for which neither full text nor verifiable CrossRef metadata could be obtained

### 2.3 Study Selection

Single-reviewer narrative screening (title → abstract → available metadata/full text), conducted in two rounds. Round 1 retained 39 sources after screening roughly 60 candidate titles across 17 WebSearch queries. Round 2 screened 289 unique candidate DOIs returned by the `paper-search` CLI across 11 queries; keyword screening (requiring a fire term and a streamflow/water term, excluding water-quality/sediment/debris-flow-only, dam/flood-engineering, and design-flood titles) reduced these to 33 on-topic candidates, of which 16 met all inclusion criteria and were added, with zero DOI overlap with Round 1. A subset of retained sources also reports on baseflow/recession alongside streamflow; they are included here because they directly report streamflow, water-yield, or peak-flow responses, not only baseflow. Combined, 55 sources were retained.

### 2.4 Data Extraction

For each source, author list, year, journal, volume/issue/pages, DOI, study location and design, and quantitative streamflow findings were extracted from search-result snippets, abstracts, and (where accessible) full text. Every DOI was independently verified against the CrossRef REST API (`api.crossref.org/works`), which confirms resolution and returns canonical author/title/venue metadata; titles, author lists, years, and venues in the bibliography were taken from these CrossRef records.

---

## 3. Results

### 3.1 Study Selection Summary

```
Round 1 (WebSearch/WebFetch): ~50 candidate titles screened
  └─ retained: n = 39
Round 2 (paper-search CLI):   289 unique candidate DOIs screened
  ├─ on-topic after keyword screen: n = 33
  └─ retained after full screening: n = 16 (0 duplicate DOIs vs. Round 1)
Combined retained corpus:     n = 55 (all DOIs CrossRef-verified)
```

The 55 retained sources comprise 8 review/synthesis articles [@ebel2025; @ebel2023; @ebelrecovery2022; @wagenbrenner2021; @collar2025; @zacharakis2026; @goeking2020; @pirani2025] and 47 primary observational or modeling studies, spanning annual water-yield/ET response, peak-flow/flood response, seasonal-timing and snow processes, recovery and management, and modeling approaches. Years range 1987–2026 (concentrated 2018–2026). Geographic coverage is dominated by the western US, with substantial Australian (Victoria, the ash forests, the Sydney Basin), Canadian (interior British Columbia), South African, and European (Portugal) case studies, plus global and CONUS-scale analyses. All sources are peer-reviewed; all 55 DOIs were independently verified (§2.4).

### 3.2 Thematic Synthesis

#### 3.2.1 Theme: Wildfire increases annual water yield, driven by reduced evapotranspiration

The most consistent finding across the corpus is that wildfire increases annual water yield in forested catchments, and that the proximate driver is reduced evapotranspiration following canopy and root loss. Early paired-catchment work established the effect and its magnitude: in South African mountain catchments, fire produced measurable increases in streamflow and large transient increases in quickflow and peak rates [@scott1993; @scott1990], while in Australian ash-eucalypt forests the *opposite-signed* long-term response was documented — dense, fast-growing regrowth after stand-replacing fire can *reduce* water yield for decades, a relationship first quantified by @kuczera1987 and reaffirmed using physiological and isotopic reasoning by @brookhouse2013. This Australian "Kuczera curve" behavior is an important early caution that the sign and persistence of the yield response depend on the post-fire vegetation trajectory, not on canopy removal alone.

In the western US, modern regional and multi-basin analyses converge on a robust short-term increase. @williams2022, comparing burned and unburned forested basins, found post-fire streamflow elevated by an average of about 30% for roughly six years where more than 20% of forest burned. @saxe2018, characterizing controls across western US watersheds, and @bart2016, providing a regional estimate for California, both found first-year increases scaling with burned fraction and antecedent wetness, with the largest responses where a large area burned under wetter conditions. @hallema2018, synthesizing 168 US fire-climate-flow records, found significant river-flow change in only 32 locations where more than ~19% of basin area burned, with the strongest enhancement in the semi-arid Lower Colorado region and negligible change in the humid Southeast — establishing that the yield response is climate- and region-contingent rather than universal. At larger watershed scales, @wine2018b found nonlinear, locally water-yield-increasing responses to combined fire and climate dynamics, and @wine2018a attributed 2–14% of long-term annual streamflow to wildfire across western US ecoregions.

The ET mechanism underlying these increases is now well constrained. @ma2020 found post-fire ET in California's Sierra Nevada reduced by ~36% in the first year and ~23% averaged over 15 years, with pre-fire canopy density and burn severity together explaining most of the spatial variation. @poon2018 documented persistent ET reduction in semi-arid burned landscapes, and @collar2021, in a CONUS-scale analysis of ~5,500 fires, showed that post-fire ET/precipitation shifts are spatially variable and strongest in the western US. @collar2022 then linked these ET shifts directly to streamflow magnitude and timing across the western US, providing a mechanistic bridge from the ET literature to the yield literature. Two studies complicate the simple "less canopy → less ET → more flow" picture: @goeking2020, synthesizing disturbance effects on western coniferous forests, found post-disturbance ET can *increase* under some severity/vegetation combinations (e.g., where surviving understory receives more radiation), and @meili2023 showed that in tall forests aerodynamic effects can *raise* ET and *deepen* water-yield reductions after fire — both reinforcing that vegetation structure and recovery, not canopy removal alone, govern the yield response. @nolan2015 similarly reported that resprouting eucalypt forests can return to or exceed pre-fire ET, depressing yield during recovery.

A critical recent caveat concerns scale and detectability. Spatial-trend analyses across western US burned watersheds likewise find heterogeneous, location-dependent streamflow trends rather than a uniform increase [@long2023]. @brown2025 found that, once paired-watershed statistical uncertainty is accounted for, percent changes in flow *magnitude* across western US burned catchments are often not statistically significant and are uncorrelated with burned fraction — even though changes in flow timing, storage, and flood events are detectable. @grillakis2025, analyzing river flows globally, likewise found diverse and regionally contingent responses rather than a single global signal. Together these argue that the well-established increase in annual yield is most reliably detected in small, heavily burned catchments and in carefully controlled regional designs, and can be obscured by local variability and concurrent weather at larger scales [@newcomer2023].

#### 3.2.2 Theme: Peak and flood flows show the largest, most hazardous responses

Wildfire's effect on peak and flood flows is far larger in relative terms than its effect on annual yield, because it operates through a different mechanism: loss of canopy interception and surface cover, soil sealing and reduced infiltration, and reduced surface roughness shift runoff toward rapid, infiltration- and saturation-excess overland flow. Foundational paired-catchment records already showed transient peak-rate increases of several hundred percent after fire [@scott1990], and synthesis work places typical post-fire flood magnitudes at one to two orders of magnitude above unburned floods for comparable storms, with extreme cases far higher [@collar2025; @pirani2025].

The magnitude of the peak-flow response is governed by burn severity, catchment morphology, and rainfall characteristics. @dilshan2025, modeling California watersheds, found a strong positive correlation between wildfire-caused discharge change and burned-area fraction. Integrated and physically based modeling sharpens the severity dependence: @li2026, using a fully distributed integrated model, found post-fire peak flows rise by ~18–29% under moderate-to-high burn severity but show almost no change under low severity, with peak-flow increase concentrated in the first post-fire precipitation events. Process-based simulations of steep catchments show that fire shifts the *timing* as well as magnitude of flood and debris-flow peaks toward flashier responses [@rengers2016], and event reconstructions reveal that the storm type (convective vs. mesoscale) controls which post-fire floods become extreme [@brogan2017]. @collar2025 synthesize the rainfall-forcing side explicitly, identifying maximum short-duration rainfall intensity as the dominant trigger and noting threshold behavior above which flood peaks rise steeply with intensity. Event-scale and basin-scale empirical analyses in Oregon [@long2022; @kang2024] and using satellite-derived burn severity [@moreno2020] confirm that post-fire peak-flow enhancement is detectable at the basin scale and scales with burn severity, while @ebelfloods2024 establishes physically based upper limits for post-fire floods and a basis for distinguishing them from debris flows. Small-catchment experimental-fire records, in which an entire instrumented catchment is deliberately burned, further isolate the runoff-generation response from confounding climate variability [@stoof2012]. @beyene2021, using a quantile-based framework that separates fire effects from post-fire weather, found large, high-severity burns raise streamflow at both the low (0.05) and high (0.95) quantiles for at least five post-fire years, with the proportional increase greatest at the lowest flows.

#### 3.2.3 Theme: Seasonal timing and low flows shift, especially in snow-dominated catchments

In snow-dominated catchments, wildfire alters not only how much water runs off but *when*. Canopy loss increases shortwave radiation reaching the snowpack, and deposition of black carbon and charred debris lowers snow albedo, so burned snowpacks melt earlier and faster. @kampf2022 documented earlier and lower-magnitude peak snow water equivalent and advanced snow-free dates at burned sites, with canopy loss driving large increases in absorbed shortwave radiation; @gleason2019 quantified a multi-fold increase in solar energy absorbed by snow beneath charred forests, persisting for many winters; and @giovando2022 and @giovando2026 showed accelerated snowmelt phenology and ecoregion-dependent SWE changes attributable to fire rather than to concurrent warming alone. @koshkin2022 review these snowpack effects, and @koshkin2025 show that snow-cover loss in burned forests is more severe in warmer, lower-elevation settings and will intensify under warming.

These snow processes translate into measurable streamflow-timing change. @biederman2022, in adjacent Lower Colorado headwaters, found post-fire summer streamflow increased by 24–38% across elevations while winter/spring flow held constant in the highest, coldest headwaters but declined at lower elevations — a clear elevation- and snow-zone-dependent repartitioning rather than a uniform increase. @spencer2024, following an extensive British Columbia wildfire, observed changes in both the quantity and timing of snow-dominated streamflow. @miller2025 found hydrologic response varied systematically between snow zones in forested headwaters, with stormflow more likely in higher, wetter snow zones. The unresolved tension, emphasized by @ebel2025, is whether earlier, faster melt augments or diminishes seasonal and annual yield, because sublimation losses and the mismatch between melt timing and infiltration capacity make peak SWE an unreliable proxy for melt volume. Low flows are similarly ambiguous: @beyene2021 found low-flow quantiles elevated for several post-fire years, consistent with reduced ET sustaining dry-season flow, but @newcomer2023 found drought could suppress any detectable wildfire signal entirely — so the low-flow response, like the timing response, is conditional on climate and snow context.

#### 3.2.4 Theme: Burn severity, climate, and vegetation recovery govern the magnitude and persistence of response

The cross-cutting lesson of the corpus is that no single number describes the fire–streamflow response; magnitude and persistence are set by interacting controls. Burn *severity* and *extent* are first-order: yield and peak-flow increases scale with burned fraction and severity [@bart2016; @dilshan2025; @li2026; @moreno2020], and below severity/area thresholds the response can be undetectable [@hallema2018; @newcomer2023]. *Climate and antecedent wetness* modulate magnitude — responses are largest in wetter years and in semi-arid regions where ET is a large fraction of the water balance [@bart2016; @hallema2018; @saxe2018] — and concurrent drought can mask the signal [@newcomer2023]. *Vegetation recovery trajectory* sets persistence: rapid resprouting or dense regrowth shortens the window of elevated yield and can even reverse it [@kuczera1987; @brookhouse2013; @nolan2015; @meili2023], whereas slow conifer recovery prolongs it.

Recovery timescales are correspondingly variable. @wine2018a found ecoregion-dependent recovery, and @williams2022 found the enhancement persists about six years on average before declining. @heath2014 tracked multi-year recovery of water yield in the Sydney Basin supply catchments after the 2001/2002 fires, and @ebelrecovery2022 provide a formal framework of approaches, metrics, and trajectories for defining "recovery" consistently across studies. @wagenbrenner2021 review Mediterranean-climate recovery specifically, and @zacharakis2026 synthesize vegetation, soil, and hydrologic recovery across settings, concluding that abiotic/hydrologic recovery is typically achieved within about a decade while vegetation structural recovery lags considerably longer. Because fire-return intervals are shortening, this recovery window increasingly overlaps with reburn, raising the prospect of compounding effects before streamflow has returned to baseline [@williams2022].

#### 3.2.5 Theme: Hydrologic models can resolve fire–streamflow response but carry structural uncertainty

Modeling is essential for attribution, projection, and extrapolation beyond the few well-gauged burned catchments, and the corpus spans the full range of approaches. Conceptual and semi-distributed models have been used to detect and attribute fire effects: @seibert2010 used a conceptual model to identify post-fire changes in snow-dominated catchment runoff, and @wells2024 used a semi-distributed model to attribute streamflow change to wildfire across multiple watersheds. Process-based and fully distributed integrated models resolve the mechanisms more explicitly — @havel2018 assessed mountainous-region response, @wampler2023 modeled Cascade Range streamflow, and @li2026 used a fully coupled surface–subsurface model to separate burn-severity and precipitation-regime effects on peak flow and ET. Emerging data-driven and remote-sensing approaches, such as @acharya2025's Landsat/UAV-based streamflow prediction, point toward operational post-fire forecasting.

Two reviews frame the modeling state of the art and its limits. @ebel2023 survey physically based distributed simulation of post-fire response and identify the representation of fire-altered infiltration, soil hydraulic properties, and rainfall forcing as the principal sources of structural uncertainty, a point reinforced by @collar2025 on the rainfall-forcing side. @brown2025's finding that magnitude changes are often statistically insignificant once paired-watershed uncertainty is propagated is, in effect, a caution for model evaluation: models calibrated to reproduce a strong mean fire signal may be overfitting variability. The recurring practical constraint across modeling studies is the scarcity of co-located observations — especially snow water equivalent and subsurface state — needed to constrain post-fire initial conditions in snow-dominated catchments [@ebel2025; @miller2025].

### 3.3 Knowledge Gaps

**Scale and detectability**: The annual-yield increase is robust in small, heavily burned catchments and controlled regional designs but is frequently undetectable at larger scales once variability is propagated [@brown2025; @grillakis2025]. Reconciling small-catchment evidence with large-basin water-supply questions remains open.

**Seasonal-timing constraint in snow zones**: Snow-process effects on melt timing are well characterized [@kampf2022; @gleason2019], but their net translation to seasonal and annual streamflow is unresolved because peak SWE is a poor proxy for melt volume [@ebel2025] and paired snow-timing/streamflow observations are rare [@miller2025; @spencer2024].

**Long-term and large-basin records**: Multi-decadal and large-basin gauge records of post-fire streamflow are scarce; most evidence is short-term and small-catchment, limiting characterization of recovery and of compounding under shortening fire-return intervals [@williams2022; @ebelrecovery2022].

**Model structural uncertainty**: Representation of fire-altered infiltration, soil hydraulic change, and rainfall forcing remains the dominant modeling uncertainty [@ebel2023; @collar2025], and post-fire initial-condition data (SWE, subsurface state) are typically lacking [@ebel2025].

---

## 4. Discussion

### 4.1 Main Findings

1. Wildfire reliably increases annual water yield in forested catchments via reduced ET, but the magnitude is strongly contingent on burn extent/severity, climate, and vegetation recovery, and the response can be statistically undetectable at large scales [@williams2022; @hallema2018; @brown2025]. Australian ash-forest cases show the response can even reverse sign over the recovery period [@kuczera1987; @brookhouse2013].
2. Peak and flood flows are the most strongly and hazardously affected component, with high-severity burns raising flood peaks for a given storm by one to two orders of magnitude through reduced infiltration and surface cover, mediated by rainfall-intensity thresholds [@collar2025; @pirani2025; @li2026; @dilshan2025].
3. In snow-dominated catchments, fire advances and accelerates snowmelt and repartitions flow seasonally, with elevation- and snow-zone-dependent effects on summer vs. winter/spring flow [@biederman2022; @spencer2024; @miller2025; @kampf2022], though the net effect on annual yield is unresolved [@ebel2025].
4. Magnitude and persistence are governed by an interacting set of controls — burn severity and extent, climate/antecedent wetness, and vegetation recovery rate — with recovery timescales ranging from a few years to over a decade [@wine2018a; @heath2014; @zacharakis2026; @ebelrecovery2022].
5. Hydrologic models from conceptual to fully integrated can attribute and project fire–streamflow response, but structural uncertainty in fire-altered infiltration and rainfall forcing, plus scarce constraining observations, limit confidence [@ebel2023; @wampler2023; @li2026; @acharya2025].

### 4.2 Interpretation and Implications

The field has strong, mechanistically grounded answers for "does wildfire increase streamflow" (yes, for annual yield and especially peak flows, in the short term) and increasingly good answers for "why" (ET reduction for yield; infiltration loss and reduced cover for peaks). The harder, more policy-relevant questions are *how much*, *for how long*, and *with what seasonal timing* at the scales water managers and flood-hazard planners actually operate. The evidence assembled here implies these answers are inherently conditional: the same fire can produce a large yield increase in a small, heavily burned, semi-arid catchment in a wet year and a statistically undetectable signal in a large, partially burned, drought-affected basin. For flood-hazard work the implication is sharper — peak-flow amplification is large, severity-dependent, and concentrated in the first post-fire storms, so the highest-value window for mitigation is immediately post-fire and in moderate-to-high-severity burns [@li2026; @collar2025]. For snow-dominated water supply, the priority is direct, paired observation of snow-timing and streamflow response across snow zones, because current peak-SWE-based proxies cannot resolve the net yield effect [@ebel2025; @miller2025].

### 4.3 Strengths and Limitations

**Strengths**: The corpus spans the full causal chain (ET and snow energy balance → infiltration and runoff generation → catchment-scale yield, peaks, and timing) and the full methodological range (paired catchments, regional multi-basin statistics, integrated process models, remote sensing), anchored by multiple recent reviews [@ebel2025; @ebel2023; @collar2025]. Geographic coverage extends well beyond the western US to Australia, Canada, South Africa, and Europe, plus CONUS- and global-scale analyses [@grillakis2025; @collar2021].

**Limitations**:
- Single-reviewer narrative screening, not independently duplicated.
- Round 1 used native WebSearch/WebFetch (substituted for `parallel-cli`); Round 2 used the `paper-search` CLI. Most full texts were paywalled, so metadata was confirmed via CrossRef rather than full-text reading, and nuanced methodological details of paywalled studies could not always be independently verified.
- Coverage remains western-US-weighted despite added international cases; tropical and boreal forest streamflow responses are under-represented.
- This is a narrative synthesis of 55 sources, not an exhaustive systematic review or meta-analysis; effect-size pooling was not attempted given the heterogeneity of designs and metrics.

### 4.4 Future Research

**Priority questions:**
1. At what catchment scale and burned fraction does the annual-yield signal become reliably detectable, and how should small-catchment evidence be upscaled to water-supply basins? — Requires paired regional designs that explicitly propagate uncertainty [@brown2025] and large-basin attribution [@grillakis2025].
2. Does fire-advanced snowmelt augment or diminish seasonal and annual yield, holding burn severity constant? — Requires co-located snow-timing and streamflow observations across snow zones [@miller2025; @spencer2024; @ebel2025].
3. How does peak-flow amplification decay through recovery, and how do reburns under shortening fire-return intervals compound it? — Requires sustained post-fire flood monitoring tied to recovery metrics [@ebelrecovery2022; @collar2025].
4. Can integrated models reduce structural uncertainty in fire-altered infiltration and rainfall forcing enough for operational post-fire streamflow and flood forecasting? — Requires better post-fire initial-condition data and continued development of process-based and data-driven approaches [@ebel2023; @li2026; @acharya2025].

---

## 5. Conclusions

1. Wildfire reliably increases post-fire annual water yield in forested catchments via evapotranspiration reduction, but the magnitude is strongly contingent on burn extent/severity, climate, and vegetation recovery, and can be undetectable at large scales or even reverse sign where dense regrowth follows stand-replacing fire [@williams2022; @brown2025; @kuczera1987].
2. Peak and flood flows are the most strongly affected component of streamflow, with high-severity burns amplifying flood peaks by one to two orders of magnitude through reduced infiltration and surface cover, concentrated in the first post-fire storms and governed by rainfall intensity [@collar2025; @pirani2025; @li2026].
3. In snow-dominated catchments, fire advances and accelerates snowmelt and repartitions flow seasonally in an elevation- and snow-zone-dependent way, but the net effect on annual yield remains unresolved owing to sublimation and timing–infiltration mismatches [@biederman2022; @kampf2022; @ebel2025].
4. The magnitude and persistence of all of these responses are set by an interacting set of controls — burn severity and extent, climate and antecedent wetness, and vegetation recovery rate — with recovery typically taking from a few years to more than a decade [@wine2018a; @zacharakis2026; @ebelrecovery2022].
5. Hydrologic models can attribute and project fire–streamflow response, but structural uncertainty in representing fire-altered infiltration and rainfall forcing, together with scarce constraining observations, currently limits confidence in quantitative prediction [@ebel2023; @wampler2023; @acharya2025].

**Evidence certainty**: High for the direction of the annual-yield increase and for large peak-flow amplification under high severity; moderate for the magnitude of yield change at basin scale; low-to-moderate for the net seasonal-timing/annual-yield effect in snow-dominated catchments.
**Translation readiness**: Intermediate — sufficient to guide post-fire flood-hazard prioritization (severity-targeted, early-window) and to motivate snow-zone instrumentation, but not yet sufficient for confident quantitative yield prediction at unstudied basins.

---

## 6. Declarations

### Data Availability
Search log and retained-source list with verified DOIs: `sources/search_log.md` (this repository). No code or data beyond search documentation was generated.

### Methodology Deviation Note
This review substituted Claude Code's native WebSearch/WebFetch tools for the `parallel-cli` tool specified as primary in the literature-review skill (Round 1), and used the `paper-search` skill/CLI (CrossRef, OpenAlex, Semantic Scholar) directly for Round 2. Because most publisher landing pages returned HTTP 402/403 to WebFetch, every retained DOI was independently verified against the CrossRef REST API rather than by full-text extraction. PDF generation tooling (pandoc, xelatex) is available and used per the skill's standard workflow.

---

## 7. References

::: {#refs}
:::
