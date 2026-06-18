# Example: Wildfire Impacts on Baseflow Dynamics in Snow-Dominated Mountain Catchments

A complete, real-world output of the `literature-review` skill, included here as a worked example.

This review investigates how wildfire affects baseflow (the groundwater-sustained low-flow
baseline) in snow-dominated mountain catchments — a narrower, more mechanistically-focused
question than the more commonly studied "does wildfire increase total streamflow."

It was produced in two search rounds, following the skill's minimum-corpus-size guidance:

1. **Round 1** — Claude Code's native `WebSearch`/`WebFetch` tools (substituting for the
   `parallel-cli` tool referenced in `SKILL.md`, which wasn't installed in that environment).
   Yielded 14 retained sources after screening ~30 candidates.
2. **Round 2** — the companion [`paper-search`](https://github.com/openags/paper-search-mcp) CLI,
   querying CrossRef, OpenAlex, and Semantic Scholar across 10 topic-targeted queries. 234 unique
   candidates were screened against the same inclusion/exclusion criteria, yielding 34 additional
   sources with zero overlap with Round 1.

**Final corpus: 48 unique sources**, all DOIs independently verified via `scripts/verify_citations.py`
(48/48 verified, 0 failed — see `wildfire_baseflow_review_citation_report.json`).

## Files

| File | Description |
|---|---|
| `wildfire_baseflow_review.md` | The full review: abstract, methodology, thematic synthesis, discussion, conclusions, and 48-entry reference list. |
| `wildfire_baseflow_review.pdf` | Rendered PDF (via `scripts/generate_pdf.py`, pandoc + xelatex), 14 pages. |
| `wildfire_baseflow_review_citation_report.json` | Output of `scripts/verify_citations.py` — every DOI, its verification status, and CrossRef-sourced metadata. |
| `sources/search_log.md` | Full search documentation for both rounds: every query run, candidate counts, and screening disposition. |

Use this example to see what a finished, citation-verified review produced by this skill actually
looks like end to end — from search log, to verification report, to final PDF.
