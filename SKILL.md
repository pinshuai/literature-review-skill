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

By default this skill is documented around the **parallel-web skill** (`parallel-cli search`) and specialized database access skills (gget, bioservices, datacommons-client) as the primary search tools — but see the Environment Note immediately below: in this environment, those are skipped in favor of Claude Code's native WebSearch/WebFetch tools. It also provides specialized tools for citation verification, result aggregation, and document generation.

## Environment Note: Tool Substitution (Always Apply)

**Skip `parallel-cli`, `gget`, and `scientific-schematics` entirely.** These tools are not installed/available in this environment. Do not attempt to install or authenticate them. Use Claude Code's native tools instead, everywhere this document references the skipped tools:

- **Web/academic search** → use the native **WebSearch** tool (pass `allowed_domains`/`blocked_domains` instead of `--include-domains`) in place of `parallel-cli search`.
- **Full-text/URL extraction** → use the native **WebFetch** tool in place of `parallel-cli extract`.
- **PubMed, bioRxiv, and other domain-specific lookups** → use WebSearch/WebFetch scoped to the relevant domain (e.g., `pubmed.ncbi.nlm.nih.gov`, `biorxiv.org`) instead of `gget search pubmed`/`gget search biorxiv`.
- **Figures/schematics** → skip the scientific-schematics step entirely. Figures are optional, not mandatory, in this environment (this overrides the "MANDATORY" language below).

Document this substitution in the review's Methodology section (as a brief transparency note), the same way the rest of the search strategy is documented. All other phases of the workflow (screening, extraction, thematic synthesis, citation verification via `scripts/verify_citations.py`, PDF generation via `scripts/generate_pdf.py`) are unaffected and should proceed as written.

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
   - **Use the native WebSearch tool for initial scoping** (see Environment Note) to quickly gauge the landscape before formal database searches

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

   Select databases appropriate for the domain. **Always start with the native WebSearch tool for broad academic coverage**, then supplement with domain-specific searches.

   **Web-Based Academic Search (native WebSearch/WebFetch — START HERE):**
   - Use the **WebSearch** tool with `allowed_domains` set to academic sources for broad scholarly coverage
   - Run two searches: academic-domain-filtered + general (no domain filter) to catch all relevant sources
     - Academic-focused: `WebSearch(query="your research topic keyword1 keyword2", allowed_domains=["arxiv.org","pubmed.ncbi.nlm.nih.gov","semanticscholar.org","biorxiv.org","medrxiv.org","ncbi.nlm.nih.gov","nature.com","science.org","ieee.org","acm.org","springer.com","wiley.com","cell.com","pnas.org","nih.gov"])`
     - General: `WebSearch(query="your research topic keyword1 keyword2")` with no domain filter
   - Save a record of each query and its returned links/snippets to `sources/search_log.md` for reproducibility (WebSearch has no `--json -o` file output, so capture results manually)
   - Use **WebFetch** to fetch full content from specific paper URLs or PDFs found in search results, e.g. `WebFetch(url="https://arxiv.org/abs/XXXX.XXXXX", prompt="Extract authors, year, DOI, abstract, and key findings")`
   - Many publisher pages (ScienceDirect, Wiley, paywalled journals) will return 403/402 via WebFetch — in that case, rely on search-result snippets plus open-access mirrors (PMC, USGS Pubs Warehouse, IOPscience, agency repositories) and note in the methodology that full text was unavailable

   **Biomedical & Life Sciences:**
   - Use **WebSearch** scoped to `pubmed.ncbi.nlm.nih.gov` / `ncbi.nlm.nih.gov` for PubMed/PMC instead of `gget search pubmed`
   - Use **WebSearch** scoped to `biorxiv.org` / `medrxiv.org` for preprints instead of `gget search biorxiv`
   - For ChEMBL, KEGG, UniProt, etc., use WebSearch/WebFetch against the relevant database's web interface if no native tool is available

   **General Scientific Literature:**
   - Search arXiv via direct API (preprints in physics, math, CS, q-bio)
   - Search Semantic Scholar via API (200M+ papers, cross-disciplinary)
   - Use Google Scholar for comprehensive coverage (manual or careful scraping)

   **Specialized Databases:**
   - For protein structures, cancer genomics, demographic/statistical data, etc., use WebSearch/WebFetch against the relevant database's web interface (e.g., alphafold.ebi.ac.uk, cancer.sanger.ac.uk/cosmic) instead of `gget`/`datacommons-client`
   - Use specialized databases as appropriate for the domain

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
   - Export results in JSON format from each database
   - Combine all results into a single file
   - Use `scripts/search_databases.py` for post-processing:
     ```bash
     python search_databases.py combined_results.json \
       --deduplicate \
       --format markdown \
       --output aggregated_results.md
     ```

### Phase 3: Screening and Selection

1. **Deduplication**:
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
     - Broaden to additional databases/sources (e.g., if only WebSearch was used, add a dedicated CLI/API tool such as the `paper-search` skill querying CrossRef/OpenAlex/Semantic Scholar, or vice versa) — different tools and sources surface different candidates even for the same topic.
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

### PubMed / PubMed Central

Access via native **WebSearch** scoped to PubMed/PMC instead of `gget`:
```
WebSearch(query="CRISPR gene editing", allowed_domains=["pubmed.ncbi.nlm.nih.gov","ncbi.nlm.nih.gov"])
```
Then **WebFetch** individual result URLs for abstracts/full text.

**Search tips**:
- Use MeSH terms: `"sickle cell disease"[MeSH]`
- Field tags: `[Title]`, `[Title/Abstract]`, `[Author]`
- Date filters: `2020:2024[Publication Date]`
- Boolean operators: AND, OR, NOT
- See MeSH browser: https://meshb.nlm.nih.gov/search

### bioRxiv / medRxiv

Access via native **WebSearch** scoped to bioRxiv/medRxiv instead of `gget`:
```
WebSearch(query="CRISPR sickle cell", allowed_domains=["biorxiv.org","medrxiv.org"])
```

**Important considerations**:
- Preprints are not peer-reviewed
- Verify findings with caution
- Check if preprint has been published (CrossRef)
- Note preprint version and date

### arXiv

Access via direct API or WebFetch:
```python
# Example search categories:
# q-bio.QM (Quantitative Methods)
# q-bio.GN (Genomics)
# q-bio.MN (Molecular Networks)
# cs.LG (Machine Learning)
# stat.ML (Machine Learning Statistics)

# Search format: category AND terms
search_query = "cat:q-bio.QM AND ti:\"single cell sequencing\""
```

### Semantic Scholar

Access via direct API (requires API key, or use free tier):
- 200M+ papers across all fields
- Excellent for cross-disciplinary searches
- Provides citation graphs and paper recommendations
- Use for finding highly influential papers

### Specialized Biomedical Databases

These rely on `gget`/`bioservices`, which are skipped in this environment (see Environment Note). Use **WebSearch**/**WebFetch** against each database's own web interface instead:
- **ChEMBL**: WebFetch against ebi.ac.uk/chembl
- **UniProt**: WebFetch against uniprot.org
- **KEGG**: WebFetch against genome.jp/kegg
- **COSMIC**: WebFetch against cancer.sanger.ac.uk/cosmic
- **AlphaFold**: WebFetch against alphafold.ebi.ac.uk
- **PDB**: WebFetch against rcsb.org

### Citation Chaining

Expand search via citation networks:

1. **Forward citations** (papers citing key papers):
   - Use **WebSearch** to find papers citing a specific work, e.g. `WebSearch(query="papers citing [Author et al. Year] [paper title]", allowed_domains=["scholar.google.com","semanticscholar.org","arxiv.org","pubmed.ncbi.nlm.nih.gov"])`
   - Use Google Scholar "Cited by"
   - Use Semantic Scholar or OpenAlex APIs
   - Identifies newer research building on seminal work

2. **Backward citations** (references from key papers):
   - Use **WebFetch** to fetch full text of key papers and extract their reference lists, e.g. `WebFetch(url="https://doi.org/10.xxxx/yyyy", prompt="List the references cited in this paper")`
   - Extract references from included papers
   - Identify highly cited foundational work
   - Find papers cited by multiple included studies

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
1. **Start with native WebSearch**: Use the WebSearch tool with `allowed_domains` set to academic sources for initial broad coverage before querying specialized databases
2. **Use multiple databases** (minimum 3): Ensures comprehensive coverage — WebSearch with different domain filters counts as separate sources
3. **Target 30-50 unique retained papers before synthesis**: Treat this as a hard gate, not a stretch goal — if the first search round retains fewer than 30 papers after screening, run additional query rounds and/or additional tools (e.g., the `paper-search` skill's CrossRef/OpenAlex/Semantic Scholar access) before moving on. See Phase 3, "Verify Minimum Corpus Size."
4. **Include preprint servers**: Captures latest unpublished findings
5. **Document everything**: Search strings, dates, result counts for reproducibility — log all WebSearch queries and results to `sources/search_log.md`
6. **Test and refine**: Run pilot searches, review results, adjust search terms
7. **Sort by citations**: When available, sort search results by citation count to surface influential work first
8. **Use WebFetch**: Fetch full content from promising URLs found during search to verify relevance before full-text screening

### Screening and Selection
1. **Use multiple databases** (minimum 3): Ensures comprehensive coverage
2. **Include preprint servers**: Captures latest unpublished findings
3. **Document everything**: Search strings, dates, result counts for reproducibility
4. **Test and refine**: Run pilot searches, review results, adjust search terms

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

# 2. Start with native WebSearch for broad academic search
# WebSearch(query="CRISPR Cas9 sickle cell disease gene therapy efficacy",
#   allowed_domains=["arxiv.org","pubmed.ncbi.nlm.nih.gov","semanticscholar.org",
#                     "biorxiv.org","nature.com","science.org","cell.com","pnas.org","nih.gov"])
# WebSearch(query="CRISPR sickle cell disease clinical trials treatment")  # general, no domain filter
# Log each query + returned links/snippets to sources/search_log.md

# 3. Search additional domains via WebSearch (PubMed, bioRxiv, arXiv, Semantic Scholar)
# - Use direct API access for arXiv, Semantic Scholar where available
# - Record results in sources/search_log.md

# 4. Aggregate and process results (combine all WebSearch results)
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

### Web Search & Extraction (native tools — PRIMARY, in this environment)
- **WebSearch**: Broad academic and general web search with domain filtering (`allowed_domains`/`blocked_domains`) — use for initial scoping, finding papers, citation chaining, and supplementary searches
- **WebFetch**: Fetch full content from paper URLs, journal websites, and preprint servers — use for reading abstracts, extracting reference lists, and verifying paper details
- `parallel-cli`, `gget`, `bioservices`, and `datacommons-client` are skipped per the Environment Note — use WebSearch/WebFetch scoped to the relevant domain instead (e.g., pubmed.ncbi.nlm.nih.gov, biorxiv.org, uniprot.org)

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

### CLI Tools (Not Required in This Environment)

`parallel-cli` and `gget` are **not used** here — see Environment Note at the top of this document. Web search and extraction are handled by Claude Code's native WebSearch/WebFetch tools, which require no installation.

### Required Python Packages
```bash
pip install requests  # For citation verification
```

### Required System Tools
```bash
# For PDF generation
brew install pandoc  # macOS
apt-get install pandoc  # Linux

# For LaTeX (PDF generation)
brew install --cask mactex  # macOS
apt-get install texlive-xetex  # Linux
```

Check dependencies:
```bash
python scripts/generate_pdf.py --check-deps
```

## Summary

This literature-review skill provides:

1. **Systematic methodology** following academic best practices
2. **Native-tool powered search** using Claude Code's WebSearch/WebFetch for broad academic literature discovery with scholarly domain filtering (no external CLI installation required)
3. **Citation verification** ensuring accuracy and credibility
4. **Professional output** in markdown and PDF formats
5. **Comprehensive guidance** covering the entire review process
6. **Quality assurance** with verification and validation tools
7. **Reproducibility** through detailed documentation requirements

Conduct thorough, rigorous literature reviews that meet academic standards and provide comprehensive synthesis of current knowledge in any domain.

