# literature-review

A [Claude Code](https://claude.com/claude-code) skill for conducting systematic, comprehensive
literature reviews — searching academic databases, screening and synthesizing findings
thematically, verifying every citation, and generating a professional PDF. This skill combines the `literature-review` skill from [K-Dense-AI](https://github.com/K-Dense-AI/scientific-agent-skills/tree/main/skills/literature-review) with the [paper-search-mcp](https://github.com/openags/paper-search-mcp) to provide more options for paper search.

A complete worked example (55 verified sources, full search log, citation report, and rendered
PDF) is included in [`examples/wildfire-streamflow/`](examples/wildfire-streamflow/).

## What it does

The skill walks through a 7-phase workflow:

1. **Planning & Scoping** — define the research question, set inclusion/exclusion criteria, and
   set a minimum corpus-size target (see [The 30–50 paper rule](#the-3050-paper-rule) below).
2. **Systematic Search** — search 20+ academic databases via the `paper-search` skill (arXiv,
   PubMed, Semantic Scholar, CrossRef, OpenAlex, bioRxiv, etc.), with WebSearch as fallback,
   documenting every query.
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
| `examples/wildfire-streamflow/` | A complete, real review produced with this skill — see its own [README](examples/wildfire-streamflow/README.md). |

**Not available — do not attempt to call**: `generate_schematic.py` and `generate_schematic_ai.py`
are not included in this package. These scripts exist in some versions of this skill but are out
of scope here. Figures are optional; use a plain markdown/ASCII PRISMA flow diagram instead
(as shown in Phase 2.5 of SKILL.md).

## Requirements

**Python** (for citation verification and search-result post-processing):
```bash
# macOS / Linux
pip install -r requirements.txt   # just `requests`

# Windows (PowerShell or cmd.exe)
py -m pip install -r requirements.txt
```
If Python itself isn't installed on Windows: `winget install --id Python.Python.3.12 -e`, or get the
installer from [python.org](https://www.python.org/downloads/) (check "Add python.exe to PATH").

**Pandoc + XeLaTeX** (for PDF generation):
```bash
# macOS
brew install pandoc
brew install --cask mactex

# Linux (Debian/Ubuntu)
apt-get install pandoc texlive-xetex

# Windows (winget — preferred)
winget install --id JohnMacFarlane.Pandoc -e
winget install --id MiKTeX.MiKTeX -e   # provides xelatex; auto-installs missing LaTeX packages on first use

# Windows (Chocolatey — alternative)
choco install pandoc miktex
```
On Windows, run PDF generation from a fresh terminal after installing MiKTeX so `xelatex` is picked
up on `PATH`. Full TeX Live (`choco install texlive`) also works but is a much larger download than
MiKTeX.

Check everything is installed (all platforms):
```bash
python scripts/generate_pdf.py --check-deps
```

### Installing dependencies as an AI agent

If you're an AI coding agent (Claude Code or otherwise) setting this skill up in a fresh
environment, don't ask the user to manually install dependencies — install them yourself:

1. Detect the OS (`platform.system()` in Python, or check for `apt-get`/`brew`/`winget`/`choco` on
   `PATH`).
2. Run `python scripts/generate_pdf.py --check-deps` first — it reports exactly which of
   `pandoc`/`xelatex` are missing, so you don't install things that are already present.
3. Install only what's missing, using the OS-appropriate commands above. Use non-interactive flags
   so the install doesn't block waiting for confirmation: `apt-get install -y ...` on Linux,
   `winget install -e --accept-source-agreements --accept-package-agreements ...` on Windows,
   `brew install ...` on macOS (Homebrew is non-interactive by default).
4. Re-run `python scripts/generate_pdf.py --check-deps` to confirm both dependencies now pass
   before starting Phase 7 of the workflow.
5. Only stop and ask the user if an install requires interactive/admin privileges you don't have
   (e.g. a UAC prompt on Windows, a `sudo` password on Linux) — don't silently skip a missing
   dependency and proceed.

Once dependencies are confirmed, follow `SKILL.md`'s 7-phase workflow to conduct the review, and
use the three scripts as documented in [Script reference](#script-reference) below —
`search_databases.py` to aggregate/dedupe search results, `verify_citations.py` to verify every DOI
before finalizing, and `generate_pdf.py` to render the finished markdown.

## Installing as a Claude Code skill

Copy (or symlink) this repo's skill files into your Claude Code skills directory, e.g.:

```bash
mkdir -p ~/.claude/skills/literature-review
cp -r SKILL.md assets references scripts ~/.claude/skills/literature-review/
```

(Use `~/.agents/skills/literature-review/` instead if your setup uses that convention.) Once
present, Claude Code will discover it automatically and you can invoke it with `/literature-review`
or by asking Claude to "conduct a literature review on ...".

## Installing the `paper-search` companion skill

`paper-search` is the **default search engine** for this skill. It queries CrossRef, OpenAlex,
Semantic Scholar, arXiv, PubMed, and 15+ other academic sources directly, returning structured JSON
with DOIs and citation counts. WebSearch/WebFetch are used as fallback when paper-search is
unavailable or a source is not covered.

### Installation (skill — recommended)

```bash
# 1. Clone the repo
git clone https://github.com/openags/paper-search-mcp.git ~/github/paper-search-mcp

# 2. Install dependencies (requires uv)
brew install uv          # macOS; or: curl -LsSf https://astral.sh/uv/install.sh | sh
uv sync --directory ~/github/paper-search-mcp

# 3. Install the Claude Code skill (substitute the actual clone path)
mkdir -p ~/.claude/skills/paper-search
REPO_PATH=~/github/paper-search-mcp   # change this if you cloned elsewhere
sed "s|<REPO_PATH>|${REPO_PATH}|g" \
  "$REPO_PATH/claude-code/SKILL.md" \
  > ~/.claude/skills/paper-search/SKILL.md

# 4. Verify
uv run --directory ~/github/paper-search-mcp paper-search sources
```

The skill is available immediately in the current session (no restart needed).

### Optional API keys (improve rate limits / unlock extra sources)

Create a `.env` file in the repo root, or set these environment variables:

| Variable | Service | Where to get it |
|---|---|---|
| `UNPAYWALL_EMAIL` | Unpaywall | Any valid email (required for Unpaywall source) |
| `CORE_API_KEY` | CORE | Free at core.ac.uk |
| `SEMANTIC_SCHOLAR_API_KEY` | Semantic Scholar | Free registration (improves rate limits) |
| `IEEE_API_KEY` | IEEE Xplore | developer.ieee.org |
| `DOAJ_API_KEY` | DOAJ | Free from doaj.org |

### AI agent installation instructions

If you are an AI coding agent setting this up in a fresh environment:

1. **Check for `uv`**: run `which uv`. If missing, install via `brew install uv` (macOS) or
   `curl -LsSf https://astral.sh/uv/install.sh | sh` (Linux/macOS).
2. **Clone the repo**: `git clone https://github.com/openags/paper-search-mcp.git ~/github/paper-search-mcp`
3. **Sync dependencies**: `uv sync --directory ~/github/paper-search-mcp`
4. **Install the skill**: copy `claude-code/SKILL.md` to `~/.claude/skills/paper-search/SKILL.md`,
   replacing every `<REPO_PATH>` with the absolute path of the clone
   (use `sed 's|<REPO_PATH>|/absolute/path|g'`).
5. **Verify**: run `uv run --directory <REPO_PATH> paper-search sources` — should print a JSON list
   of available sources with no errors.
6. The skill is active immediately; no Claude Code restart required.

### Running searches

The `paper-search` CLI is invoked via `uv run` from the cloned source repo:

```bash
REPO=~/github/paper-search-mcp

# Search (primary method for every literature review)
uv run --directory $REPO paper-search search \
  "your topic keywords" -n 15 -s arxiv,semantic,crossref,openalex

# Biomedical sources
uv run --directory $REPO paper-search search \
  "your topic keywords" -n 15 -s pubmed,pmc,biorxiv,medrxiv

# With year filter
uv run --directory $REPO paper-search search \
  "your topic keywords" -n 15 -s semantic -y 2020-2025

# Read full text of a paper
uv run --directory $REPO paper-search read arxiv 2401.12345

# List all available sources
uv run --directory $REPO paper-search sources
```

- `-n` — results per source (default 5; use 15–20 for systematic reviews)
- `-s` — comma-separated sources, or `all`. Prefer targeted source sets over `all` for speed.
- `-y` — year filter applied to Semantic Scholar results (e.g. `"2020"` or `"2018-2022"`)

If `uv` or the repo are unavailable, fall back to WebSearch with `allowed_domains` set to
academic sources — see `SKILL.md` for the fallback syntax.

`paper-search` is especially useful for building a corpus systematically — see the example's
[`sources/search_log.md`](examples/wildfire-streamflow/sources/search_log.md) for how a
two-round search (WebSearch round 1 + `paper-search` round 2) reached 55 retained papers.


## Example

[`examples/wildfire-streamflow/`](examples/wildfire-streamflow/) is a complete review — *"Wildfire
Impacts on Streamflow in Forested Catchments"* — produced end to end with this skill and the following prompt:

```
/literature-review how does wildfire impact streamflow?
```

Use it as a reference for what each workflow phase's output should actually look like.

## Script reference

| Script | What it does |
|---|---|
| `verify_citations.py <review.md>` | Extracts every DOI from the markdown, resolves it via `doi.org`, fetches metadata from the CrossRef API, and writes `<review>_citation_report.json` with verified/failed lists and formatted citations. |
| `generate_pdf.py <review.md> [output.pdf] [--citation-style STYLE] [--no-toc] [--no-numbers] [--check-deps]` | Wraps `pandoc --pdf-engine=xelatex` to render the markdown as a styled, paginated PDF. |
| `search_databases.py <results.json> [--deduplicate] [--rank citations\|year\|relevance] [--year-start Y] [--year-end Y] [--format json\|markdown\|bibtex] [--output FILE] [--summary]` | Post-processes raw search-result JSON: dedupe by DOI/title, rank, year-filter, and reformat. |

## License

MIT — see [`LICENSE`](LICENSE). Skill authored by K-Dense Inc.
