---
name: literature-review
description: Conduct comprehensive, systematic literature reviews using multiple academic databases (PubMed, arXiv, bioRxiv, Semantic Scholar, etc.). This skill should be used when conducting systematic literature reviews, meta-analyses, research synthesis, or comprehensive literature searches across biomedical, scientific, and technical domains. Creates professionally formatted markdown documents and PDFs with verified citations in multiple citation styles (APA, Nature, Vancouver, etc.).
allowed-tools: Read Write Edit Bash
license: MIT license
required_environment_variables: [{"name": "OPENROUTER_API_KEY", "prompt": "OpenRouter API key for the skill's LLM-powered steps.", "required_for": "optional features"}]
metadata: {"version": "1.2", "skill-author": "K-Dense Inc.", "openclaw": {"primaryEnv": "OPENROUTER_API_KEY", "envVars": [{"name": "OPENROUTER_API_KEY", "required": false, "description": "OpenRouter API key for the skill's LLM-powered steps."}]}}
---

# Literature Review

## Overview

Conduct systematic, comprehensive literature reviews following rigorous academic methodology. Search multiple literature databases, synthesize findings thematically, verify all citations for accuracy, and generate professional output documents in markdown and PDF formats.

This skill uses the **`paper-search` skill** as the primary search engine and falls back to Claude Code's native **WebSearch/WebFetch** tools when paper-search is unavailable or returns insufficient results. It also provides specialized tools for citation verification, result aggregation, and document generation.

## Search Engine Priority (Always Apply)

**Use `paper-search` first for all academic literature searches.** It queries 20+ databases (arXiv, PubMed, Semantic Scholar, CrossRef, OpenAlex, bioRxiv, and more) with a single command, returns structured JSON with DOIs and citation counts, and is far more comprehensive than web search for finding peer-reviewed literature.

```bash
# Primary: paper-search (structured, multi-database, returns JSON with DOIs)
uv run --directory /Users/shuai/github/paper-search-mcp paper-search search \
  "<query>" -n 15 -s arxiv,semantic,crossref,openalex,pubmed

# Full-text of a specific paper
uv run --directory /Users/shuai/github/paper-search-mcp paper-search read <source> <paper_id>
```

**Fall back to WebSearch/WebFetch when:**
- `paper-search` is unavailable (repo missing, `uv` not installed)
- A source is not covered by paper-search (e.g., paywalled journal pages, specialized databases like ChEMBL/UniProt/AlphaFold)
- Citation chaining requires fetching a specific URL
- The corpus is still below 30 papers after all paper-search rounds

**WebSearch fallback syntax:**
```
WebSearch(query="...", allowed_domains=["arxiv.org","pubmed.ncbi.nlm.nih.gov","semanticscholar.org","biorxiv.org","nature.com","agupubs.onlinelibrary.wiley.com"])
WebFetch(url="https://doi.org/10.xxxx/yyyy", prompt="Extract title, authors, year, abstract, key findings")
```

**Figures/schematics**: skip `scientific-schematics` entirely — figures are optional. Use a plain markdown/ASCII PRISMA flow diagram instead.

Document the search tools used in the review's Methodology section. All other phases (screening, extraction, thematic synthesis, citation verification via `scripts/verify_citations.py`, PDF generation via `scripts/generate_pdf.py`) are unaffected and should proceed as written.

## When to Use This Skill

Use this skill when:
- Conducting a systematic literature review for research or publication
- Synthesizing current knowledge on a specific topic across multiple sources
- Performing meta-analysis or scoping reviews
- Writing the literature review section of a research paper or thesis
- Investigating the state of the art in a research domain
- Identifying research gaps and future directions
- Requiring verified citations and professional formatting

## Visual Enhancement with Scientific Schematics (Skipped in This Environment)

**Skipped per the Environment Note above** — `scientific-schematics` is not available, and figures are optional, not mandatory, here. If a PRISMA-style flow diagram is useful, render it as a plain markdown/ASCII flow (as already used in Phase 2.5) rather than generating an image. Do not attempt to call `scripts/generate_schematic.py` or the scientific-schematics skill.

---

## Core Workflow

Literature reviews follow a structured, multi-phase workflow:

### Phase 1: Planning and Scoping

1. **Define Research Question**: Use PICO framework (Population, Intervention, Comparison, Outcome) for clinical/biomedical reviews
   - Example: "What is the efficacy of CRISPR-Cas9 (I) for treating sickle cell disease (P) compared to standard care (C)?"

2. **Establish Scope and Objectives**:
   - Define clear, specific research questions
   - Determine review type (narrative, systematic, scoping, meta-analysis)
   - Set boundaries (time period, geographic scope, study types)

3. **Develop Search Strategy**:
   - Identify 2-4 main concepts from research question
   - List synonyms, abbreviations, and related terms for each concept
   - Plan Boolean operators (AND, OR, NOT) to combine terms
   - Select minimum 3 complementary databases
   - **Use `paper-search` for initial scoping** (see Search Engine Priority above) to quickly gauge the landscape; fall back to WebSearch if paper-search is unavailable

4. **Set Inclusion/Exclusion Criteria**:
   - Date range (e.g., last 10 years: 2015-2024)
   - Language (typically English, or specify multilingual)
   - Publication types (peer-reviewed, preprints, reviews)
   - Study designs (RCTs, observational, in vitro, etc.)
   - Document all criteria clearly

5. **Set Minimum Corpus Size Target (CRITICAL — required before proceeding to synthesis)**:
   - **Target at least 30-50 unique, retained (post-screening) papers** before moving on to Phase 5 (Synthesis). A handful of single-round WebSearch queries will rarely clear this bar on their own.
   - If the topic is genuinely narrow and exhaustive searching still yields fewer than 30 retained papers, document this explicitly in the Methodology section (with the search rounds/queries that were run) rather than silently proceeding with a small corpus.
   - See Phase 3, "Verify Minimum Corpus Size" for what to do if the first search round falls short.

### Phase 2: Systematic Literature Search

1. **Multi-Database Search**:

   Select databases appropriate for the domain. **Always start with `paper-search`** for structured, multi-database academic coverage. Fall back to WebSearch/WebFetch only when paper-search cannot cover a source or is unavailable (see Search Engine Priority above).

   **Primary: paper-search CLI (START HERE)**
   - Run 2–4 targeted queries covering your main concepts and synonyms, targeting the most relevant sources for your domain.
   - **The `search` command outputs JSON to stdout — always redirect with `>` to save results.**
     ```bash
     # General academic (good starting point for any domain)
     uv run --directory /Users/shuai/github/paper-search-mcp paper-search search \
       "keyword1 keyword2 keyword3" -n 15 -s arxiv,semantic,crossref,openalex \
       > results_round1a.json

     # Biomedical focus
     uv run --directory /Users/shuai/github/paper-search-mcp paper-search search \
       "keyword1 keyword2" -n 15 -s pubmed,pmc,biorxiv,medrxiv,europepmc \
       > results_round1b.json

     # With year filter (Semantic Scholar)
     uv run --directory /Users/shuai/github/paper-search-mcp paper-search search \
       "keyword1 keyword2" -n 15 -s semantic -y 2020-2025 \
       > results_round1c.json
     ```
   - Log each query (string, sources, `-n`, result count) to `sources/search_log.md`
   - To read full text of a promising paper: `uv run ... paper-search read <source> <paper_id>`
   - List all available sources: `uv run ... paper-search sources`

   **Fallback: WebSearch/WebFetch**

   Use when paper-search is unavailable, or to supplement for sources it doesn't cover:
   - Academic-focused web search:
     ```
     WebSearch(query="keyword1 keyword2", allowed_domains=["arxiv.org","pubmed.ncbi.nlm.nih.gov","semanticscholar.org","biorxiv.org","nature.com","science.org","agupubs.onlinelibrary.wiley.com","hess.copernicus.org"])
     ```
   - General (no domain filter): `WebSearch(query="keyword1 keyword2")`
   - Full-text fetch: `WebFetch(url="https://arxiv.org/abs/XXXX.XXXXX", prompt="Extract authors, year, DOI, abstract, key findings")`
   - Many publisher pages (ScienceDirect, Wiley, paywalled journals) return 403/402 via WebFetch — rely on open-access mirrors (PMC, IOPscience, agency repositories) and note in methodology that full text was unavailable

   **Specialized Databases (WebFetch only)**
   - For sources not covered by paper-search (ChEMBL, UniProt, KEGG, AlphaFold, COSMIC, PDB): use WebFetch against the database's own web interface

2. **Document Search Parameters**:
   ```markdown
   ## Search Strategy

   ### Database: PubMed
   - **Date searched**: 2024-10-25
   - **Date range**: 2015-01-01 to 2024-10-25
   - **Search string**:
     ```
     ("CRISPR"[Title] OR "Cas9"[Title])
     AND ("sickle cell"[MeSH] OR "SCD"[Title/Abstract])
     AND 2015:2024[Publication Date]
     ```
   - **Results**: 247 articles
   ```

   Repeat for each database searched.

3. **Export and Aggregate Results**:
   - Each `paper-search` query saves a separate JSON file (via `>` redirection above). Merge all per-query files into one before processing:
     ```bash
     # Merge multiple JSON arrays into one (requires jq)
     jq -s 'add' results_*.json > combined_results.json

     # Or without jq: open each file in Python and concatenate the lists, then write combined_results.json
     ```
   - Then post-process with `scripts/search_databases.py`:
     ```bash
     python scripts/search_databases.py combined_results.json \
       --deduplicate \
       --rank citations \
       --format markdown \
       --output aggregated_results.md
     ```

### Phase 3: Screening and Selection

1. **Deduplication**:
   - paper-search returns JSON per query; combine and deduplicate by DOI across all rounds before screening
   ```bash
   python search_databases.py results.json --deduplicate --output unique_results.json
   ```
   - Removes duplicates by DOI (primary) or title (fallback)
   - Document number of duplicates removed

2. **Title Screening**:
   - Review all titles against inclusion/exclusion criteria
   - Exclude obviously irrelevant studies
   - Document number excluded at this stage

3. **Abstract Screening**:
   - Read abstracts of remaining studies
   - Apply inclusion/exclusion criteria rigorously
   - Document reasons for exclusion

4. **Full-Text Screening**:
   - Obtain full texts of remaining studies
   - Conduct detailed review against all criteria
   - Document specific reasons for exclusion
   - Record final number of included studies

5. **Create PRISMA Flow Diagram**:
   ```
   Initial search: n = X
   ├─ After deduplication: n = Y
   ├─ After title screening: n = Z
   ├─ After abstract screening: n = A
   └─ Included in review: n = B
   ```

6. **Verify Minimum Corpus Size (CRITICAL — do this before Phase 4)**:
   - Check the final retained count (`n = B` above) against the **30-50 unique paper target** set in Phase 1.
   - **If `n < 30`**: do not proceed to synthesis yet. Run additional search rounds first:
     - Add more queries covering sub-topics, synonyms, and related mechanisms not yet covered by the initial query set.
     - Broaden to additional databases/sources (e.g., add `-s pubmed,europepmc,doaj` if you only queried `arxiv,semantic` so far, or switch to WebSearch as fallback) — different sources surface different candidates even for the same topic.
     - Use citation chaining (forward/backward citations, see below) from the strongest papers already retained.
     - Re-screen the new candidates against the same inclusion/exclusion criteria, dedupe against the existing set (by DOI), and re-check the total.
   - Repeat additional search rounds until the retained total reaches 30-50 (more is fine; there is no hard ceiling), or until you can document that the topic is genuinely too narrow to support that many peer-reviewed sources.
   - Log every additional search round (queries, tool used, candidates found, candidates retained) in `sources/search_log.md`, exactly as the first round was logged.

### Phase 4: Data Extraction and Quality Assessment

1. **Extract Key Data** from each included study:
   - Study metadata (authors, year, journal, DOI)
   - Study design and methods
   - Sample size and population characteristics
   - Key findings and results
   - Limitations noted by authors
   - Funding sources and conflicts of interest

2. **Assess Study Quality**:
   - **For RCTs**: Use Cochrane Risk of Bias tool
   - **For observational studies**: Use Newcastle-Ottawa Scale
   - **For systematic reviews**: Use AMSTAR 2
   - Rate each study: High, Moderate, Low, or Very Low quality
   - Consider excluding very low-quality studies

3. **Organize by Themes**:
   - Identify 3-5 major themes across studies
   - Group studies by theme (studies may appear in multiple themes)
   - Note patterns, consensus, and controversies

### Phase 5: Synthesis and Analysis

1. **Create Review Document** from template:
   ```bash
   cp assets/review_template.md my_literature_review.md
   ```

2. **Write Thematic Synthesis** (NOT study-by-study summaries):
   - Organize Results section by themes or research questions
   - Synthesize findings across multiple studies within each theme
   - Compare and contrast different approaches and results
   - Identify consensus areas and points of controversy
   - Highlight the strongest evidence

   Example structure:
   ```markdown
   #### 3.3.1 Theme: CRISPR Delivery Methods

   Multiple delivery approaches have been investigated for therapeutic
   gene editing. Viral vectors (AAV) were used in 15 studies^1-15^ and
   showed high transduction efficiency (65-85%) but raised immunogenicity
   concerns^3,7,12^. In contrast, lipid nanoparticles demonstrated lower
   efficiency (40-60%) but improved safety profiles^16-23^.
   ```

3. **Critical Analysis**:
   - Evaluate methodological strengths and limitations across studies
   - Assess quality and consistency of evidence
   - Identify knowledge gaps and methodological gaps
   - Note areas requiring future research

4. **Write Discussion**:
   - Interpret findings in broader context
   - Discuss clinical, practical, or research implications
   - Acknowledge limitations of the review itself
   - Compare with previous reviews if applicable
   - Propose specific future research directions

### Phase 6: Citation Verification

**CRITICAL**: All citations must be verified for accuracy before final submission.

1. **Verify All DOIs**:
   ```bash
   python scripts/verify_citations.py my_literature_review.md
   ```

   This script:
   - Extracts all DOIs from the document
   - Verifies each DOI resolves correctly
   - Retrieves metadata from CrossRef
   - Generates verification report
   - Outputs properly formatted citations

2. **Review Verification Report**:
   - Check for any failed DOIs
   - Verify author names, titles, and publication details match
   - Correct any errors in the original document
   - Re-run verification until all citations pass

3. **Format Citations Consistently**:
   - Choose one citation style and use throughout (see `references/citation_styles.md`)
   - Common styles: APA, Nature, Vancouver, Chicago, IEEE
   - Use verification script output to format citations correctly
   - Ensure in-text citations match reference list format

4. **Use pandoc citation keys so citations hyperlink to the reference list (REQUIRED for linked PDFs)**:

   Hand-typed author-year strings like `(Smith et al., 2023)` plus a hand-numbered
   reference list produce **no clickable links** in the PDF — pandoc has no way to
   connect the prose to the reference entries. To get in-text citations that link to
   their reference entry (and reference entries that link to their DOI), author the
   review with pandoc's citation syntax and let `citeproc` build the reference list:

   - **In-text citations** use `[@key]` (parenthetical) or `@key` (narrative), e.g.
     `[@smith2023]` → "(Smith et al., 2023)", `@smith2023` → "Smith et al. (2023)".
     Multiple: `[@smith2023; @jones2024]`. Page/prefix: `[see @smith2023, p. 5]`.
   - **A bibliography database** (`my_literature_review.bib`) sits next to the markdown
     and holds one BibTeX entry per source, keyed by the same `key`. Build it from the
     verified metadata — `scripts/search_databases.py --format bibtex` emits BibTeX, and
     `scripts/verify_citations.py` confirms every DOI first. One stable convention for
     keys is first-author-surname + year (+ `a`/`b` to disambiguate), e.g. `smith2023`.
   - **The reference section** is auto-generated: end the document with a `# References`
     heading (citeproc appends the formatted, linked list after it). Do **not** also
     hand-write a numbered list — citeproc replaces it.
   - `scripts/generate_pdf.py` auto-detects `my_literature_review.bib` and enables
     `--citeproc` with `link-citations`/`link-bibliography`, so every `[@key]` becomes an
     internal hyperlink. (Without a `.bib`, it falls back to rendering the markdown as-is,
     and citations will not link.)

### Phase 7: Document Generation

1. **Generate PDF**:
   ```bash
   python scripts/generate_pdf.py my_literature_review.md \
     --citation-style apa \
     --output my_review.pdf
   ```

   Options:
   - `--citation-style`: apa, nature, chicago, vancouver, ieee
   - `--no-toc`: Disable table of contents
   - `--no-numbers`: Disable section numbering
   - `--check-deps`: Check if pandoc/xelatex are installed

2. **Review Final Output**:
   - Check PDF formatting and layout
   - Verify all sections are present
   - Ensure citations render correctly **and click through to the reference list**
     (requires the `.bib` + `[@key]` convention from Phase 6, step 4)
   - Check that figures/tables appear properly
   - Verify table of contents is accurate

   Note: if the review's headings are manually numbered (e.g. `## 7. References`), pass
   `--no-numbers` so pandoc doesn't add a second layer of section numbers.

3. **Quality Checklist**:
   - [ ] At least 30-50 unique, retained papers included (or the shortfall is explicitly justified in Methodology)
   - [ ] All DOIs verified with verify_citations.py
   - [ ] Citations formatted consistently
   - [ ] PRISMA flow diagram included (for systematic reviews)
   - [ ] Search methodology fully documented
   - [ ] Inclusion/exclusion criteria clearly stated
   - [ ] Results organized thematically (not study-by-study)
   - [ ] Quality assessment completed
   - [ ] Limitations acknowledged
   - [ ] References complete and accurate
   - [ ] In-text citations hyperlink to the reference list in the PDF (`.bib` + `[@key]` convention)
   - [ ] PDF generates without errors

## Database-Specific Search Guidance

### paper-search sources reference

`paper-search` covers these sources directly (pass via `-s`):

| Source key | Database | Best for |
|---|---|---|
| `arxiv` | arXiv | Physics, CS, math, q-bio preprints |
| `pubmed` | PubMed | Biomedical literature |
| `pmc` | PubMed Central | Open-access biomedical full text |
| `semantic` | Semantic Scholar | Cross-disciplinary, citation counts |
| `crossref` | CrossRef | DOI resolution, metadata |
| `openalex` | OpenAlex | Open, 200M+ works |
| `biorxiv` / `medrxiv` | bioRxiv / medRxiv | Life science / medical preprints |
| `europepmc` | Europe PMC | European biomedical |
| `doaj` | DOAJ | Open-access journals |
| `core` | CORE | Open-access full text (needs `CORE_API_KEY`) |
| `zenodo` | Zenodo | Data, software, grey literature |
| `dblp` | DBLP | Computer science |
| `hal` | HAL | French research (open access) |

For speed, use targeted source sets rather than `all`:
- **Physical/earth sciences**: `-s arxiv,semantic,crossref,openalex`
- **Biomedical**: `-s pubmed,pmc,biorxiv,medrxiv,europepmc,semantic`
- **Computer science / ML**: `-s arxiv,semantic,dblp,crossref`

**Sources not listed above** (ChEMBL, UniProt, KEGG, AlphaFold, PDB, COSMIC) are not covered by paper-search — use WebFetch against their web interfaces (see "Specialized Databases (WebFetch only)" below).

### PubMed / PubMed Central

**Primary**: `uv run ... paper-search search "<query>" -s pubmed,pmc -n 15`

**Fallback** (WebSearch): `WebSearch(query="...", allowed_domains=["pubmed.ncbi.nlm.nih.gov","ncbi.nlm.nih.gov"])`

**Search tips**:
- Use MeSH terms in your query string: e.g. `"sickle cell disease" gene therapy`
- Boolean: AND, OR, NOT work in the query string
- See MeSH browser: https://meshb.nlm.nih.gov/search

### bioRxiv / medRxiv

**Primary**: `uv run ... paper-search search "<query>" -s biorxiv,medrxiv -n 10`

**Fallback**: `WebSearch(query="...", allowed_domains=["biorxiv.org","medrxiv.org"])`

Preprints are not peer-reviewed — verify findings with caution and check if the preprint has since been published (CrossRef).

### arXiv

**Primary**: `uv run ... paper-search search "<query>" -s arxiv -n 15`

**Fallback** (WebFetch to arXiv API):
```
WebFetch(url="https://export.arxiv.org/search/?query=your+terms&searchtype=all&start=0&max_results=20", prompt="List paper titles, authors, arXiv IDs, and abstracts")
```

### Semantic Scholar

**Primary**: `uv run ... paper-search search "<query>" -s semantic -n 15 -y 2020-2025`

Returns citation counts — use these to surface highly-cited seminal work quickly.

**Note**: Without a `SEMANTIC_SCHOLAR_API_KEY`, Semantic Scholar may return 0 results due to rate limiting. Set the key as an environment variable (or in a `.env` in the paper-search repo root) for reliable results — free registration at semanticscholar.org.

### Specialized Databases (WebFetch only)

These are not covered by paper-search. Use WebFetch against their web interfaces:
- **ChEMBL**: WebFetch against ebi.ac.uk/chembl
- **UniProt**: WebFetch against uniprot.org
- **KEGG**: WebFetch against genome.jp/kegg
- **AlphaFold**: WebFetch against alphafold.ebi.ac.uk
- **PDB**: WebFetch against rcsb.org

### Citation Chaining

Expand search via citation networks:

1. **Forward citations** (papers citing key papers):
   - `uv run ... paper-search search "title of key paper author year" -s semantic,openalex -n 10`
   - Or WebSearch: `WebSearch(query="papers citing [Author Year Title]", allowed_domains=["semanticscholar.org","arxiv.org"])`
   - Identifies newer research building on seminal work

2. **Backward citations** (references from key papers):
   - `uv run ... paper-search read <source> <paper_id>` — extracts full text including references
   - Or WebFetch: `WebFetch(url="https://doi.org/10.xxxx/yyyy", prompt="List the references cited in this paper")`
   - Find papers cited by multiple included studies — these are likely foundational

## Citation Style Guide

Detailed formatting guidelines are in `references/citation_styles.md`. Quick reference:

### APA (7th Edition)
- In-text: (Smith et al., 2023)
- Reference: Smith, J. D., Johnson, M. L., & Williams, K. R. (2023). Title. *Journal*, *22*(4), 301-318. https://doi.org/10.xxx/yyy

### Nature
- In-text: Superscript numbers^1,2^
- Reference: Smith, J. D., Johnson, M. L. & Williams, K. R. Title. *Nat. Rev. Drug Discov.* **22**, 301-318 (2023).

### Vancouver
- In-text: Superscript numbers^1,2^
- Reference: Smith JD, Johnson ML, Williams KR. Title. Nat Rev Drug Discov. 2023;22(4):301-18.

**Always verify citations** with verify_citations.py before finalizing.

### Prioritizing High-Impact Papers (CRITICAL)

**Always prioritize influential, highly-cited papers from reputable authors and top venues.** Quality matters more than quantity in literature reviews.

#### Citation Count Thresholds

Use citation counts to identify the most impactful papers:

| Paper Age | Citation Threshold | Classification |
|-----------|-------------------|----------------|
| 0-3 years | 20+ citations | Noteworthy |
| 0-3 years | 100+ citations | Highly Influential |
| 3-7 years | 100+ citations | Significant |
| 3-7 years | 500+ citations | Landmark Paper |
| 7+ years | 500+ citations | Seminal Work |
| 7+ years | 1000+ citations | Foundational |

#### Journal and Venue Tiers

Prioritize papers from higher-tier venues:

- **Tier 1 (Always Prefer):** Nature, Science, Cell, NEJM, Lancet, JAMA, PNAS, Nature Medicine, Nature Biotechnology
- **Tier 2 (Strong Preference):** High-impact specialized journals (IF>10), top conferences (NeurIPS, ICML for ML/AI)
- **Tier 3 (Include When Relevant):** Respected specialized journals (IF 5-10)
- **Tier 4 (Use Sparingly):** Lower-impact peer-reviewed venues

#### Author Reputation Assessment

Prefer papers from:
- **Senior researchers** with high h-index (>40 in established fields)
- **Leading research groups** at recognized institutions (Harvard, Stanford, MIT, Oxford, etc.)
- **Authors with multiple Tier-1 publications** in the relevant field
- **Researchers with recognized expertise** (awards, editorial positions, society fellows)

#### Identifying Seminal Papers

For any topic, identify foundational work by:
1. **High citation count** (typically 500+ for papers 5+ years old)
2. **Frequently cited by other included studies** (appears in many reference lists)
3. **Published in Tier-1 venues** (Nature, Science, Cell family)
4. **Written by field pioneers** (often cited as establishing concepts)

## Best Practices

### Search Strategy
1. **Start with `paper-search`**: Run 2–4 targeted queries against relevant source sets (`-s arxiv,semantic,crossref,openalex` for most topics; add domain-specific sources as needed). Fall back to WebSearch only if paper-search is unavailable or a source isn't covered.
2. **Use multiple source sets** (minimum 3 databases): Ensures comprehensive coverage — use different `-s` combinations to hit complementary databases
3. **Target 30-50 unique retained papers before synthesis**: Treat this as a hard gate — if the first round retains fewer than 30 papers, run additional rounds with more queries and/or different source sets before moving on. See Phase 3, "Verify Minimum Corpus Size."
4. **Include preprint servers**: Add `-s biorxiv,medrxiv,arxiv` to capture latest unpublished findings
5. **Document everything**: Log every query (search string, sources, `-n`, result count, retained count) to `sources/search_log.md`
6. **Test and refine**: Run a pilot query first, review results, then adjust terms before running the full query set
7. **Sort by citations**: paper-search returns citation counts for Semantic Scholar results — prioritize highly-cited papers; use `search_databases.py --rank citations` to sort aggregated results
8. **Use `paper-search read`**: Fetch full text of promising papers to verify relevance before committing to full-text screening

### Screening and Selection
1. **Use clear criteria**: Document inclusion/exclusion criteria before screening
2. **Screen systematically**: Title → Abstract → Full text
3. **Document exclusions**: Record reasons for excluding studies
4. **Consider dual screening**: For systematic reviews, have two reviewers screen independently

### Synthesis
1. **Organize thematically**: Group by themes, NOT by individual studies
2. **Synthesize across studies**: Compare, contrast, identify patterns
3. **Be critical**: Evaluate quality and consistency of evidence
4. **Identify gaps**: Note what's missing or understudied

### Quality and Reproducibility
1. **Assess study quality**: Use appropriate quality assessment tools
2. **Verify all citations**: Run verify_citations.py script
3. **Document methodology**: Provide enough detail for others to reproduce
4. **Follow guidelines**: Use PRISMA for systematic reviews

### Writing
1. **Be objective**: Present evidence fairly, acknowledge limitations
2. **Be systematic**: Follow structured template
3. **Be specific**: Include numbers, statistics, effect sizes where available
4. **Be clear**: Use clear headings, logical flow, thematic organization

## Common Pitfalls to Avoid

1. **Single database search**: Misses relevant papers; always search multiple databases
2. **No search documentation**: Makes review irreproducible; document all searches
3. **Study-by-study summary**: Lacks synthesis; organize thematically instead
4. **Unverified citations**: Leads to errors; always run verify_citations.py
5. **Too broad search**: Yields thousands of irrelevant results; refine with specific terms
6. **Too narrow search**: Misses relevant papers; include synonyms and related terms
7. **Ignoring preprints**: Misses latest findings; include bioRxiv, medRxiv, arXiv
8. **No quality assessment**: Treats all evidence equally; assess and report quality
9. **Publication bias**: Only positive results published; note potential bias
10. **Outdated search**: Field evolves rapidly; clearly state search date
11. **Stopping at a single search round with <30 retained papers**: A first pass of a handful of queries against one tool often yields only 10-15 retained papers; this is a screening-coverage problem, not evidence that the literature is sparse. Run additional rounds — more queries, additional databases/tools, citation chaining — until the 30-50 paper target (Phase 1) is met, or explicitly document why the topic cannot support it.

## Example Workflow

Complete workflow for a biomedical literature review:

```bash
# 1. Create review document from template
cp assets/review_template.md crispr_sickle_cell_review.md

# 2. PRIMARY: paper-search across key source sets
REPO=/Users/shuai/github/paper-search-mcp

uv run --directory $REPO paper-search search \
  "CRISPR Cas9 sickle cell disease gene therapy" \
  -n 15 -s arxiv,semantic,crossref,openalex > results_round1a.json

uv run --directory $REPO paper-search search \
  "CRISPR base editing hemoglobin beta thalassemia" \
  -n 15 -s pubmed,pmc,biorxiv,medrxiv,europepmc > results_round1b.json

# Log each query + source set + result count to sources/search_log.md

# 3. FALLBACK: WebSearch if paper-search misses specific sources
# WebSearch(query="CRISPR sickle cell clinical trials 2023 2024",
#   allowed_domains=["pubmed.ncbi.nlm.nih.gov","nature.com","cell.com","nejm.org"])
# Log queries + returned links/snippets to sources/search_log.md

# 4. Aggregate and process results (combine all paper-search JSON + any WebSearch results)
python scripts/search_databases.py combined_results.json \
  --deduplicate \
  --rank citations \
  --year-start 2015 \
  --year-end 2024 \
  --format markdown \
  --output search_results.md \
  --summary

# 5. Screen results and extract data
# - Use WebFetch to fetch full content from promising URLs
# - Manually screen titles, abstracts, full texts
# - Extract key data into the review document
# - Organize by themes

# 6. Write the review following template structure
# - Introduction with clear objectives
# - Detailed methodology section
# - Results organized thematically
# - Critical discussion
# - Clear conclusions

# 7. Verify all citations
python scripts/verify_citations.py crispr_sickle_cell_review.md

# Review the citation report
cat crispr_sickle_cell_review_citation_report.json

# Fix any failed citations and re-verify
python scripts/verify_citations.py crispr_sickle_cell_review.md

# 8. Generate professional PDF
python scripts/generate_pdf.py crispr_sickle_cell_review.md \
  --citation-style nature \
  --output crispr_sickle_cell_review.pdf

# 9. Review final PDF and markdown outputs
```

## Integration with Other Skills

This skill works seamlessly with other scientific skills:

### Academic Search (in priority order)
- **`paper-search` skill** (PRIMARY): structured multi-database search via `uv run ... paper-search search` — covers arXiv, PubMed, Semantic Scholar, CrossRef, OpenAlex, bioRxiv, and 15+ more sources. Use for all initial and supplementary literature searches.
- **WebSearch** (FALLBACK): broad web search with `allowed_domains` filtering — use when paper-search is unavailable or a specific source isn't covered (e.g., paywalled journals, specialized databases)
- **WebFetch** (SUPPLEMENTARY): fetch full content from specific paper URLs, journal pages, or preprint servers — use for reading abstracts, extracting reference lists, and verifying paper details

### Analysis Skills
- **pydeseq2**: RNA-seq differential expression (for methods sections)
- **scanpy**: Single-cell analysis (for methods sections)
- **anndata**: Single-cell data (for methods sections)
- **biopython**: Sequence analysis (for background sections)

### Visualization Skills
- **matplotlib**: Generate figures and plots for review
- **seaborn**: Statistical visualizations

### Writing Skills
- **brand-guidelines**: Apply institutional branding to PDF
- **internal-comms**: Adapt review for different audiences
- **venue-templates**: Access venue-specific writing style guides when preparing reviews for publication

### Venue-Specific Writing Styles

When preparing a literature review for a specific journal, consult the **venue-templates** skill for writing style guidance:
- `venue_writing_styles.md`: Master style comparison across venues
- `nature_science_style.md`: Nature/Science flowing abstract style, story-driven structure
- `cell_press_style.md`: Cell Press graphical abstracts, Highlights format
- `medical_journal_styles.md`: NEJM/Lancet/JAMA structured abstracts, PRISMA compliance

These guides help adapt your review's tone, abstract format, and structure to match the target venue's expectations.

## Resources

### Bundled Resources

**Scripts:**
- `scripts/verify_citations.py`: Verify DOIs and generate formatted citations
- `scripts/generate_pdf.py`: Convert markdown to professional PDF
- `scripts/search_databases.py`: Process, deduplicate, and format search results

**References:**
- `references/citation_styles.md`: Detailed citation formatting guide (APA, Nature, Vancouver, Chicago, IEEE)
- `references/database_strategies.md`: Comprehensive database search strategies

**Assets:**
- `assets/review_template.md`: Complete literature review template with all sections

### External Resources

**Guidelines:**
- PRISMA (Systematic Reviews): http://www.prisma-statement.org/
- Cochrane Handbook: https://training.cochrane.org/handbook
- AMSTAR 2 (Review Quality): https://amstar.ca/

**Tools:**
- MeSH Browser: https://meshb.nlm.nih.gov/search
- PubMed Advanced Search: https://pubmed.ncbi.nlm.nih.gov/advanced/
- Boolean Search Guide: https://www.ncbi.nlm.nih.gov/books/NBK3827/

**Citation Styles:**
- APA Style: https://apastyle.apa.org/
- Nature Portfolio: https://www.nature.com/nature-portfolio/editorial-policies/reporting-standards
- NLM/Vancouver: https://www.nlm.nih.gov/bsd/uniform_requirements.html

## Dependencies

### CLI Tools

- **`paper-search`** (PRIMARY): install via `git clone https://github.com/openags/paper-search-mcp` + `uv sync`, then invoke as `uv run --directory <REPO_PATH> paper-search search ...`. See the `paper-search` skill (`~/.claude/skills/paper-search/SKILL.md`) for full usage.
- `parallel-cli` and `gget` are **not used** — replaced by `paper-search` and the native WebSearch/WebFetch fallback.

### Required Python Packages
```bash
pip install requests  # For citation verification
```

### Required System Tools
```bash
# For PDF generation
brew install pandoc  # macOS
apt-get install pandoc  # Linux
winget install --id JohnMacFarlane.Pandoc -e  # Windows

# For LaTeX (PDF generation)
brew install --cask mactex  # macOS
apt-get install texlive-xetex  # Linux
winget install --id MiKTeX.MiKTeX -e  # Windows — provides xelatex
```

Check dependencies:
```bash
python scripts/generate_pdf.py --check-deps
```

**If you are the agent running this skill**: do not stop and ask the user to install these —
detect the OS and install missing dependencies yourself with the commands above (non-interactively:
`apt-get install -y`, `winget install -e --accept-source-agreements --accept-package-agreements`),
then re-run `--check-deps` to confirm before proceeding to Phase 7. Only ask the user if the install
needs interactive elevation (sudo password, UAC prompt) you don't have.

## Summary

This literature-review skill provides:

1. **Systematic methodology** following academic best practices
2. **`paper-search`-first search** querying 20+ academic databases (arXiv, PubMed, Semantic Scholar, CrossRef, OpenAlex, and more) via a single CLI, with WebSearch/WebFetch as fallback
3. **Citation verification** ensuring accuracy and credibility
4. **Professional output** in markdown and PDF formats
5. **Comprehensive guidance** covering the entire review process
6. **Quality assurance** with verification and validation tools
7. **Reproducibility** through detailed documentation requirements

Conduct thorough, rigorous literature reviews that meet academic standards and provide comprehensive synthesis of current knowledge in any domain.

