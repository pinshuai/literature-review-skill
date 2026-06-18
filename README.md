# literature-review

A [Claude Code](https://claude.com/claude-code) skill for conducting systematic, comprehensive
literature reviews — searching academic databases, screening and synthesizing findings
thematically, verifying every citation, and generating a professional PDF.

A complete worked example (48 verified sources, full search log, citation report, and rendered
PDF) is included in [`examples/wildfire-baseflow/`](examples/wildfire-baseflow/).

## What it does

The skill walks through a 7-phase workflow:

1. **Planning & Scoping** — define the research question, set inclusion/exclusion criteria, and
   set a minimum corpus-size target (see [The 30–50 paper rule](#the-3050-paper-rule) below).
2. **Systematic Search** — search multiple databases/sources (web search, PubMed, arXiv,
   Semantic Scholar, CrossRef, OpenAlex, etc.), documenting every query.
3. **Screening & Selection** — deduplicate, screen by title/abstract/full-text against the stated
   criteria, and verify the corpus meets the minimum size target before moving on.
4. **Data Extraction & Quality Assessment** — extract key data from each included study and rate
   study quality.
5. **Synthesis & Analysis** — write a **thematic** synthesis (not a study-by-study summary),
   organized around 3–5 major themes.
6. **Citation Verification** — every DOI is independently verified against CrossRef before the
   review is considered final.
7. **Document Generation** — render the finished markdown into a styled PDF.

Full details live in [`SKILL.md`](SKILL.md), which is the skill definition Claude Code reads.

## Repo contents

| Path | Purpose |
|---|---|
| `SKILL.md` | The skill definition — full workflow, database guidance, citation styles, best practices. |
| `assets/review_template.md` | Fill-in-the-blanks template for a complete review manuscript (Abstract → References → Appendices). |
| `references/citation_styles.md` | APA / Nature / Chicago / Vancouver / IEEE formatting reference. |
| `references/database_strategies.md` | Database-by-database search strategies (PubMed, arXiv, Semantic Scholar, biomedical databases, etc.). |
| `scripts/verify_citations.py` | Extracts DOIs from a review, verifies each against CrossRef, and writes a `*_citation_report.json`. |
| `scripts/generate_pdf.py` | Converts the finished markdown into a PDF via `pandoc` + `xelatex`. |
| `scripts/search_databases.py` | Deduplicates, ranks, year-filters, and reformats raw search-result JSON. |
| `examples/wildfire-baseflow/` | A complete, real review produced with this skill — see its own [README](examples/wildfire-baseflow/README.md). |

**Not included**: this package omits the optional AI-schematic-generation scripts
(`generate_schematic.py` / `generate_schematic_ai.py`) that exist in some versions of this skill.
They require an `OPENROUTER_API_KEY` and generate figures via an external image model — out of
scope for this package. Figures are optional in any case; a plain markdown/ASCII PRISMA flow
diagram is sufficient.

## Requirements

**Python** (for citation verification and search-result post-processing):
```bash
pip install -r requirements.txt   # just `requests`
```

**Pandoc + XeLaTeX** (for PDF generation):
```bash
# macOS
brew install pandoc
brew install --cask mactex

# Linux
apt-get install pandoc texlive-xetex
```

Check everything is installed:
```bash
python scripts/generate_pdf.py --check-deps
```

## Installing as a Claude Code skill

Copy (or symlink) this repo's skill files into your Claude Code skills directory, e.g.:

```bash
mkdir -p ~/.claude/skills/literature-review
cp -r SKILL.md assets references scripts ~/.claude/skills/literature-review/
```

(Use `~/.agents/skills/literature-review/` instead if your setup uses that convention.) Once
present, Claude Code will discover it automatically and you can invoke it with `/literature-review`
or by asking Claude to "conduct a literature review on ...".

## Installing the `paper-search` companion CLI

`SKILL.md` documents `parallel-cli` as the primary search tool, with a fallback to Claude Code's
native `WebSearch`/`WebFetch` tools when `parallel-cli` isn't installed. A third option — used for
the "Round 2" deep-search pass in the bundled example — is the
[`paper-search`](https://github.com/openags/paper-search-mcp) CLI, which queries CrossRef,
OpenAlex, Semantic Scholar, arXiv, PubMed, and 15+ other academic sources directly.

To install it:

```bash
git clone https://github.com/openags/paper-search-mcp.git /path/to/paper-search-mcp
cd /path/to/paper-search-mcp
uv sync   # or: pip install -e .
```

Run a search:

```bash
uv run --directory /path/to/paper-search-mcp paper-search search \
  "your topic keywords" -n 15 -s crossref,openalex,semantic
```

- `-n` — results per source (default 5)
- `-s` — comma-separated sources, or `all` (default: all). For speed, prefer targeted sources
  (`crossref,openalex,semantic`) over `all`.
- `-y` — optional year filter (e.g. `2020` or `2018-2022`), applied to Semantic Scholar results

`paper-search` is especially useful for systematically extending a review that came up short on
the minimum corpus size after an initial WebSearch-only pass — see the example's
[`sources/search_log.md`](examples/wildfire-baseflow/sources/search_log.md) for exactly how this
played out (Round 1: WebSearch → 14 sources; Round 2: `paper-search` → 34 more, 48 total).

## Usage

```bash
# 1. Start from the template
cp assets/review_template.md my_review.md

# 2. Search — document every query in sources/search_log.md
#    (WebSearch/WebFetch, and/or paper-search, and/or domain-specific databases)

# 3. Screen candidates against your inclusion/exclusion criteria.
#    Keep searching additional queries/sources until the retained corpus is >=30 papers
#    (see "The 30-50 paper rule" below) — or document explicitly why the topic can't support it.

# 4. Write the review: thematic synthesis (not study-by-study), following the template structure.

# 5. Verify every citation
python scripts/verify_citations.py my_review.md
cat my_review_citation_report.json   # check for any failed DOIs; fix and re-run until 0 failed

# 6. Generate the PDF
python scripts/generate_pdf.py my_review.md --citation-style apa

# 7. Tick the quality checklist in the "Review Metadata" section of the template
#    only once each item is genuinely true.
```

### The 30–50 paper rule

A single round of a handful of search queries against one tool will often retain only 10–15
papers after screening — this is a **search-coverage problem**, not evidence the literature is
sparse. The skill treats **30–50 unique, retained papers** as the target corpus size before
moving on to synthesis:

- If the first round falls short, run additional query rounds, add another search tool/database
  (e.g. add `paper-search` if you started with WebSearch, or vice versa), and/or use citation
  chaining (forward/backward citations from your strongest papers).
- Log every additional round in `sources/search_log.md`, exactly like the first round.
- If a genuinely narrow topic still can't reach 30 after exhaustive searching, say so explicitly
  in the Methodology section rather than silently shipping a small corpus.

See `SKILL.md` → Phase 1 step 5 and Phase 3 step 6 for the full guidance, and the bundled example
for a case where this rule was applied to go from 14 → 48 papers across two search rounds.

## Worked example

[`examples/wildfire-baseflow/`](examples/wildfire-baseflow/) is a complete review — *"Wildfire
Impacts on Baseflow Dynamics in Snow-Dominated Mountain Catchments"* — produced end to end with
this skill:

- **48 unique sources**, built from a 14-paper WebSearch round plus a 34-paper `paper-search` CLI
  round (234 candidates screened, 0 duplicates between rounds).
- **48/48 DOIs verified**, 0 failed (`wildfire_baseflow_review_citation_report.json`).
- Full search documentation for both rounds (`sources/search_log.md`).
- A rendered 14-page PDF (`wildfire_baseflow_review.pdf`).

Use it as a reference for what each workflow phase's output should actually look like.

## Script reference

| Script | What it does |
|---|---|
| `verify_citations.py <review.md>` | Extracts every DOI from the markdown, resolves it via `doi.org`, fetches metadata from the CrossRef API, and writes `<review>_citation_report.json` with verified/failed lists and formatted citations. |
| `generate_pdf.py <review.md> [output.pdf] [--citation-style STYLE] [--no-toc] [--no-numbers] [--check-deps]` | Wraps `pandoc --pdf-engine=xelatex` to render the markdown as a styled, paginated PDF. |
| `search_databases.py <results.json> [--deduplicate] [--rank citations\|year\|relevance] [--year-start Y] [--year-end Y] [--format json\|markdown\|bibtex] [--output FILE] [--summary]` | Post-processes raw search-result JSON: dedupe by DOI/title, rank, year-filter, and reformat. |

## License

MIT — see [`LICENSE`](LICENSE). Skill authored by K-Dense Inc.
