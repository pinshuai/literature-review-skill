# Example: Wildfire Impacts on Streamflow in Forested Catchments

A complete, real-world output of the `literature-review` skill, included here as a worked example.

This review investigates how wildfire affects streamflow — annual water yield, peak/flood flows,
and seasonal timing (including snow-zone effects) — in forested and montane catchments. This is
the broader, more commonly studied question than the companion question of wildfire's effect on
baseflow specifically (the groundwater-sustained low-flow baseline), which this skill's older
example covered.

It was produced in two search rounds, following the skill's minimum-corpus-size guidance:

1. **Round 1** — Claude Code's native `WebSearch`/`WebFetch` tools (substituting for the
   `parallel-cli` tool referenced in `SKILL.md`, which wasn't installed in that environment).
   17 queries surfaced ~60 candidate titles, yielding 39 retained sources after screening.
2. **Round 2** — the companion [`paper-search`](https://github.com/openags/paper-search-mcp) skill,
   querying CrossRef, OpenAlex, and Semantic Scholar across 11 topic-targeted queries. 289 unique
   candidates (after removing Round 1 overlap) were screened against the same inclusion/exclusion
   criteria, narrowed to 33 on-topic titles, yielding 16 additional sources with zero DOI overlap
   with Round 1.

**Final corpus: 55 unique sources**, all DOIs independently verified via `scripts/verify_citations.py`
(55/55 verified, 0 failed — see `wildfire_streamflow_review_citation_report.json`). Most publisher
landing pages (Wiley, ScienceDirect, IOP, MDPI) returned HTTP 402/403 to WebFetch, so every DOI was
confirmed against the CrossRef REST API instead of full-text extraction — documented in the
Methodology Deviation Note in the review itself.

The review uses pandoc citation keys (`[@key]`) backed by a `.bib` bibliography, so in the PDF
**every in-text citation is a clickable internal link to its reference entry, and every reference
entry links to its DOI** (see the skill's [citation guidance](../../references/citation_styles.md#pandoc-citations-for-linked-pdfs)).

## Files

| File | Description |
|---|---|
| `wildfire_streamflow_review.md` | The full review: abstract, methodology, thematic synthesis (annual water-yield/ET change, peak-flow/flood response, seasonal-timing/snow-zone effects, severity-climate-recovery controls, modeling approaches), discussion, conclusions. In-text citations are pandoc `[@key]` keys; the reference list is generated automatically by citeproc at build time (the source ends with a `::: {#refs}` placeholder). |
| `wildfire_streamflow_review.bib` | BibTeX bibliography (55 entries, full author lists, CrossRef-verified DOIs) that resolves every `[@key]`. |
| `apa.csl` | APA 7th-edition citation style (from the [CSL styles repo](https://github.com/citation-style-language/styles)); `generate_pdf.py --citation-style apa` picks it up. |
| `wildfire_streamflow_review.pdf` | Rendered PDF (via `scripts/generate_pdf.py`, pandoc + xelatex), 15 pages, with hyperlinked citations and DOIs. |
| `wildfire_streamflow_review_citation_report.json` | Output of `scripts/verify_citations.py` run against the `.bib` — every DOI, its verification status, and CrossRef-sourced metadata. |
| `sources/search_log.md` | Full search documentation for both rounds: every query run, candidate counts, and screening disposition. |

Use this example to see what a finished, citation-verified review produced by this skill actually
looks like end to end — from search log, to verification report, to a final PDF with working
citation links.

To rebuild the PDF yourself:

```bash
python ../../scripts/verify_citations.py wildfire_streamflow_review.bib   # 55/55 verified
python ../../scripts/generate_pdf.py wildfire_streamflow_review.md --citation-style apa --no-numbers
```

(`--no-numbers` is used because this review's section headings are numbered by hand.)
