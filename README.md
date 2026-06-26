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
| `examples/wildfire-streamflow/` | A complete, real review produced with this skill — see its own [README](examples/wildfire-streamflow/README.md). |

**Not included**: this package omits the optional AI-schematic-generation scripts
(`generate_schematic.py` / `generate_schematic_ai.py`) that exist in some versions of this skill.
They require an `OPENROUTER_API_KEY` and generate figures via an external image model — out of
scope for this package. Figures are optional in any case; a plain markdown/ASCII PRISMA flow
diagram is sufficient.

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

## Installing the `paper-search` companion CLI

Paper search is done through the
[`paper-search`](https://github.com/openags/paper-search-mcp) CLI, which queries CrossRef,
OpenAlex, Semantic Scholar, arXiv, PubMed, and 15+ other academic sources directly. The fallback is using the native web search which may not give good coverage and results.

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
[`sources/search_log.md`](examples/wildfire-streamflow/sources/search_log.md) for exactly how this
played out (Round 1: WebSearch → 39 sources; Round 2: `paper-search` → 16 more, 55 total).


## Example

[`examples/wildfire-streamflow/`](examples/wildfire-streamflow/) is a complete review — *"Wildfire
Impacts on Streamflow in Forested Catchments"* — produced end to end with this skill and the following prompt:

```
/literature-review how does wildfire impact streamflow?
```

## License

MIT — see [`LICENSE`](LICENSE). Skill authored by K-Dense Inc.
