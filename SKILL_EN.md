---
name: literature-review
description: |
  Literature Review Skill — A systematic paper survey and literature review tool for Claude Code.
  Use when the user wants to conduct a systematic literature review on a research area, technical direction, or algorithmic family. Core workflow: define scope -> systematic retrieval -> taxonomy construction -> method comparison -> trend analysis -> open problems & future directions. Outputs a professionally typeset PDF survey report.
  Trigger phrases include but are not limited to: paper survey, literature review, survey this area, related work, what papers exist on, review the literature, summarize the field, state of the art in, what's the current progress on.
  Even if the user simply asks "what papers are out there on X" or "how far has this field come", trigger this skill as long as context suggests a systematic review is needed.
  Do NOT use for: reading/summarizing a single paper (just read it directly), blog or social media writing, commercial product/company analysis.
---

# Systematic Literature Review

> **Methodology**
> This skill conducts a systematic paper survey on a given research area, producing a well-structured, comprehensive literature review report. Core principles: broad retrieval, systematic taxonomy, in-depth comparison, trend distillation.

You are conducting a systematic literature review. The final deliverable is a **professionally typeset PDF survey report**.

## Prerequisites

### Environment Setup

1. **Ensure the PDF conversion script is available**: This skill includes `scripts/md_to_pdf.py` (based on WeasyPrint) to convert the final Markdown report into a well-formatted PDF. Install dependencies: `pip install weasyprint markdown --break-system-packages`.

### Define the Review Scope

After receiving user input, confirm the following. If the user's request is already specific enough (e.g., "survey the papers on multi-agent collaboration"), skip clarification and start immediately:

1. **Research topic**: The specific research direction, problem, or technique
2. **Time range** (optional): Which period to focus on? Default: last 3-5 years; seminal/foundational works are not time-limited
3. **Focus area** (optional): Theory-oriented / application-oriented / specific sub-direction?
4. **Exclusions** (optional): Any sub-areas to explicitly exclude?

---

## Step 1: Systematic Paper Retrieval

The quality of a literature review depends on retrieval coverage. **Online search is mandatory** -- do not rely solely on pre-existing knowledge.

### Parallel Retrieval Strategy

Use sub-agents in parallel for efficient retrieval. Recommended division of labor:

- **Sub-Agent 1 -- arXiv retrieval**: Systematically search arXiv API for relevant papers, collecting titles, authors, abstracts, publication dates, and citation relationships
- **Sub-Agent 2 -- Academic search & survey discovery**: Use Google Scholar, Semantic Scholar, etc. to discover highly-cited papers, existing survey papers, and key research groups
- **Sub-Agent 3** (only for complex domains): Supplementary retrieval via GitHub awesome-lists, top venue accepted paper lists, research group homepages, and technical blog posts

**Sub-Agent web access guide** (include verbatim in each sub-agent prompt):

Each sub-agent prompt must contain the following retrieval instructions:

> You need to fetch paper information from the web. Use these tools:
> - **WebSearch**: Search for papers, discover information sources
> - **WebFetch**: Visit specific URLs to extract content
> - **arXiv API**: `curl -s "https://export.arxiv.org/api/query?search_query=all:keyword1+AND+all:keyword2&start=0&max_results=30&sortBy=submittedDate&sortOrder=descending"` -- returns XML with title, authors, abstract, date, PDF link. Adjust keyword combinations and result count as needed
> - **Semantic Scholar API**: `curl -s "https://api.semanticscholar.org/graph/v1/paper/search?query=keywords&limit=20&fields=title,authors,year,abstract,citationCount,venue"` -- fetches papers with citation counts
> - Try multiple searches with varied keyword combinations; do not give up after one query
> - After finding key papers, use WebFetch to read the paper page for more details
> - Watch for existing survey/review papers in the field -- they are important references

Frame prompts around goals ("retrieve", "investigate", "discover"), letting sub-agents choose the best approach autonomously.

### Retrieval Source Priority

| Source | Purpose |
|--------|---------|
| arXiv | Latest preprints; covers CS/AI/Physics/Math etc. |
| Semantic Scholar | Citation networks, highly-cited paper discovery |
| Google Scholar | Broad coverage, cross-disciplinary discovery |
| Top venues (NeurIPS, ICML, ICLR, ACL, CVPR, etc.) | Peer-reviewed high-quality work |
| GitHub awesome-lists | Community-curated paper lists; catches gaps |
| Existing survey papers | Reference their taxonomy and coverage |

### Retrieval Completeness Checklist

After retrieval, verify:
- Have the seminal/foundational papers in the field been found?
- Are highly-cited papers covered (citation > 100, or relatively high for the subfield)?
- Are recent developments (past 6-12 months) included?
- Are representative works from major research groups/labs covered?
- Are different technical approaches and methodological schools represented?

If papers are insufficient, search again. Over-collect now, filter later.

---

## Step 2: Taxonomy Construction

This is the skeleton of the survey and determines the report's organizational structure.

### Taxonomy Strategies

Choose the most appropriate classification dimension based on the field's characteristics:

**By method/technical approach**: Most common. E.g., "rule-based vs. learning-based vs. hybrid methods".

**By problem/task**: Suitable when a broad area encompasses multiple sub-tasks.

**By temporal evolution**: Suitable for fields with clear generational progression (e.g., 1st generation -> 2nd generation -> 3rd generation).

**Hybrid**: First classify by broad technical approach, then subdivide each approach by sub-problem or timeline.

### Taxonomy Requirements

- The taxonomy must be **MECE (Mutually Exclusive, Collectively Exhaustive)**: every paper should fit into exactly one category; categories should not overlap significantly
- If a paper spans multiple categories, place it in its primary category and cross-reference in others
- Limit hierarchy depth to 3 levels to avoid fragmentation
- Visualize the taxonomy tree (using Markdown indentation or tables) for quick comprehension

---

## Step 3: In-Depth Analysis of Each Method Category

This is the main body of the report. Introduce each method category following the taxonomy.

### Required Coverage per Category

**Core idea**: What is the fundamental approach? What is the key insight behind this class of methods?

**Representative papers**: List the 3-5 most important papers in the category, each including:
- Paper title, authors, year, and venue
- Core contribution (1-3 sentence summary; do not copy the abstract)
- Key technical details (methodological highlights, not full derivations)
- Experimental highlights (which benchmarks, what results)

**Evolution trajectory**: How have methods within this category evolved? What did follow-up work improve over prior work?

**Strengths and limitations**: What are the advantages? What are the inherent limitations? When is this approach suitable vs. unsuitable?

### Length

1,500-4,000 words per major category, adjusted for paper count and complexity. Step 3 total: 5,000-15,000 words. Key papers deserve detailed discussion; secondary papers can be mentioned briefly.

---

## Step 4: Cross-Method Comparative Analysis

Systematically compare methods across all categories.

### Comparison Dimensions

Select dimensions based on the field's characteristics. Common ones include:

| Dimension | Description |
|-----------|-------------|
| Performance | Results on mainstream benchmarks (tabulated) |
| Computational efficiency | Training/inference time and resource costs |
| Data requirements | How much data? What type? Labeling requirements? |
| Scalability | Can it scale to larger problem sizes? |
| Interpretability | Are results explainable and debuggable? |
| Generalization | Cross-domain/cross-task transfer ability |
| Deployment readiness | Engineering maturity and production feasibility |

### Comparison Methods

- **Comparison tables**: Present key dimensions across methods at a glance
- **Qualitative analysis**: Beyond tables, analyze trade-offs and scenario-specific method selection
- **Benchmark aggregation**: If the field has established benchmarks, aggregate numerical comparisons

### Length

2,000-5,000 words.

---

## Step 5: Research Trends & Open Problems

This is the synthesis section that demonstrates analytical depth.

### Trend Analysis

- **Methodological evolution over time**: How has the research paradigm shifted from early work to present?
- **Current hotspots**: Which directions have the densest publication activity in the past 1-2 years? What questions are the community most focused on?
- **Cross-disciplinary convergence**: Are there new ideas imported from other fields making an impact?

### Open Problems & Future Directions

- **Unsolved core challenges**: What are the biggest bottlenecks in this field today?
- **Promising new directions**: Based on current trends, where might breakthroughs occur?
- **Research-to-practice gap**: What is the disconnect between academic research and real-world deployment?
- **Infrastructure needs**: Are there missing benchmarks, datasets, or evaluation standards?

### Length

1,500-3,000 words.

---

## Writing Style

This is an academic survey, but it should not be a dry catalog of papers. The goal is to give readers a **panoramic understanding of the entire field** in a single document.

### Core Principles

**Narrative-driven, not list-driven**: Do not write "Paper A did X, Paper B did Y, Paper C did Z" sequentially. Explain the connections between methods -- why did A lead to B? What problem of A did B solve? What new perspective did C bring?

**Precise summarization**: Capture each paper's core contribution in 1-3 sentences. After reading your summary, a reader should be able to decide whether they need to read the original.

**Take positions**: Offer judgments on method strengths/weaknesses and field trends, but ground every judgment in evidence. Clearly label speculation as such.

**Researcher-oriented utility**: After reading the survey, a researcher should be able to answer: How far has this field progressed? What are the mainstream approaches? What are their respective pros and cons? If I want to work in this area, where should I start?

### Pitfalls to Avoid

- Do not simply list paper abstracts; provide your own synthesis and interpretation
- Do not shy away from pointing out limitations; honestly address each method's shortcomings
- Do not omit important related work; coverage is the fundamental value of a survey
- If information cannot be found, honestly mark it as "information unavailable" -- never fabricate
- Avoid generic AI filler phrases: "In summary", "It is worth noting", "It is not difficult to see", "In today's rapidly evolving landscape of X"

---

## Step 6: Generate PDF Report

After completing the report, use the included `scripts/md_to_pdf.py` to convert Markdown into a professionally typeset PDF.

### Conversion Workflow

1. **Complete the Markdown draft**: Write the full report in standard Markdown format, saved as `[topic]_paper_survey.md`
2. **Install dependencies** (if not already installed): `pip install weasyprint markdown --break-system-packages`
3. **Run the conversion script**:
   ```bash
   python [skill_directory]/scripts/md_to_pdf.py input.md output.pdf --title "Topic" --subtitle "Paper Survey" --author "Author Name"
   ```
4. The script generates an intermediate HTML file (for debugging) and the final PDF

### Markdown Formatting Guidelines

For optimal PDF output from the conversion script:

- Start with `# Title` as the report title (automatically used on the cover page)
- Immediately after the title, optionally use `> Field: ... | Period: ... | Papers: ...` for metadata (extracted to the cover)
- Use `##` for major section headings
- Use `###` and `####` for subsections
- Use standard Markdown table syntax
- Use `>` for block quotes
- Citation format: **Author (Year)**. Title. *Venue*.

### Report Structure Template

```
Cover Page

1. Introduction
[Research background, motivation, scope statement; 500-1,000 words]

2. Taxonomy
[Classification tree + classification logic; 500-1,000 words]

3. Detailed Method Analysis
  3.1 Method Category A
    3.1.1 Core Idea
    3.1.2 Representative Papers
    3.1.3 Evolution Trajectory
    3.1.4 Strengths & Limitations
  3.2 Method Category B
  ...
[5,000-15,000 words]

4. Cross-Method Comparison
[Comparison tables + qualitative analysis; 2,000-5,000 words]

5. Research Trends & Open Problems
[Trend analysis + future directions; 1,500-3,000 words]

6. Conclusion
[Key findings summary; 500-1,000 words]

References
[Complete citation list]
```

### File Naming and Delivery

Name the PDF `[topic]_paper_survey.pdf` and save it to the user's working directory.

---

## Length Overview

| Section | Word Count | Notes |
|---------|-----------|-------|
| Introduction | 500 - 1,000 | Background and scope |
| Taxonomy | 500 - 1,000 | Clear classification system |
| Detailed Method Analysis | 5,000 - 15,000 | Report body |
| Cross-Method Comparison | 2,000 - 5,000 | Systematic comparison |
| Trends & Open Problems | 1,500 - 3,000 | Insights and outlook |
| Conclusion | 500 - 1,000 | Key takeaways |
| **Total** | **10,000 - 25,000** | Depth and coverage are the core value |

---

## Quality Checklist

Pre-delivery self-check:

- [ ] Paper retrieval covered major sources (arXiv, top venues, Semantic Scholar)?
- [ ] Seminal and highly-cited papers are not missing?
- [ ] Recent developments (past 12 months) are included?
- [ ] Taxonomy is logically sound and MECE?
- [ ] Core contribution of each important paper is accurately summarized?
- [ ] Evolution and connections between methods are clearly articulated?
- [ ] Cross-method comparison includes both tables and qualitative analysis?
- [ ] Research trends are grounded in actual publication data, not vague assertions?
- [ ] Open problems are specific and actionable?
- [ ] The report offers original synthesis, not just abstract listings?
- [ ] Missing information is honestly marked as unavailable, with nothing fabricated?
- [ ] References are complete (author, year, title, venue)?
- [ ] PDF is well-formatted with clear structure?
