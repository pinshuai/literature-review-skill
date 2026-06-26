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

See [AI agent: complete setup](#ai-agent-complete-setup) below for the full installation procedure, including both skill installs and all dependency verification steps.

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

See [AI agent: complete setup](#ai-agent-complete-setup) below for the full end-to-end procedure that installs both this skill and the `paper-search` companion skill with verification steps.

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


## AI agent: complete setup

If you are an AI coding agent setting up this skill in a fresh environment, follow these steps in order. Do not ask the user to run any of them — detect the environment and execute them yourself.

### Step 1 — Install the `literature-review` skill

```bash
# Determine repo root (works whether invoked from inside the repo or by absolute path)
LR_REPO=$(git -C "$(dirname "$0")" rev-parse --show-toplevel 2>/dev/null || echo "$PWD")

mkdir -p ~/.claude/skills/literature-review
cp -r "$LR_REPO/SKILL.md" "$LR_REPO/assets" "$LR_REPO/references" "$LR_REPO/scripts" \
  ~/.claude/skills/literature-review/
```

**Verify**: `ls ~/.claude/skills/literature-review/SKILL.md` — must exist with no error.

### Step 2 — Install `uv` (required for paper-search)

```bash
# macOS
which uv || brew install uv

# Linux / macOS (universal installer)
which uv || curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Verify**: `uv --version` — must print a version string (e.g. `uv 0.x.x`).

### Step 3 — Clone and set up the `paper-search` repo

```bash
PS_REPO=~/github/paper-search-mcp

# Clone if not present; pull to update if already cloned
git clone https://github.com/openags/paper-search-mcp.git "$PS_REPO" 2>/dev/null \
  || git -C "$PS_REPO" pull --ff-only

uv sync --directory "$PS_REPO"
```

**Verify**: `uv sync` must complete without errors.

### Step 4 — Install the `paper-search` skill

```bash
mkdir -p ~/.claude/skills/paper-search

# realpath resolves ~ and symlinks so the path in the skill file is absolute
PS_REPO_ABS=$(realpath ~/github/paper-search-mcp)

sed "s|<REPO_PATH>|${PS_REPO_ABS}|g" \
  "$PS_REPO_ABS/claude-code/SKILL.md" \
  > ~/.claude/skills/paper-search/SKILL.md
```

**Verify** that `<REPO_PATH>` was fully substituted (result must be 0):

```bash
grep -c '<REPO_PATH>' ~/.claude/skills/paper-search/SKILL.md
# Expected output: 0
```

### Step 5 — Verify `paper-search` CLI works

```bash
uv run --directory ~/github/paper-search-mcp paper-search sources
```

**Expected**: a JSON array listing 15+ source names (arxiv, pubmed, semantic, crossref, openalex, ...).  
**If it errors**: confirm `uv sync` in Step 3 completed cleanly; check that `uv` is on `PATH`.

### Step 6 — Install Python dependencies

```bash
pip install requests   # or: pip install -r "$LR_REPO/requirements.txt"
```

**Verify**: `python -c "import requests; print('OK')"` prints `OK`.

### Step 7 — Install pandoc + xelatex (Phase 7 PDF generation only)

```bash
python ~/.claude/skills/literature-review/scripts/generate_pdf.py --check-deps
```

If either dependency is reported missing, install it with the OS-appropriate command from the [Requirements](#requirements) section above. Use non-interactive flags (`-y`, `--accept-source-agreements`, etc.). Only pause and ask the user if the install requires interactive admin/sudo credentials you do not have.

Re-run `--check-deps` after installing to confirm both `pandoc` and `xelatex` pass before proceeding to Phase 7.

### Step 8 — End-to-end smoke test

Run a minimal search to confirm the full stack works:

```bash
uv run --directory ~/github/paper-search-mcp paper-search search \
  "machine learning hydrology" -n 2 -s arxiv > /tmp/lr_smoke_test.json

python ~/.claude/skills/literature-review/scripts/search_databases.py \
  /tmp/lr_smoke_test.json --summary
```

**Expected**: summary output showing ≥ 1 result from arxiv.  
**If 0 results**: check network access; arXiv is available without any API key.  

Both skills are active immediately — no Claude Code restart required.

---

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
