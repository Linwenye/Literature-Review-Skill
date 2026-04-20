# Literature Review Skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill designed specifically for **systematic literature reviews and paper surveys** -- the kind of "Related Work" section you'd write for an academic paper, but as a comprehensive standalone report.

## What It Does

Given a research topic, this skill instructs Claude Code to:

1. **Systematically retrieve papers** from arXiv, Semantic Scholar, Google Scholar, and top venues using parallel sub-agents
2. **Build a MECE taxonomy** -- classify all collected papers into a clear, logically organized hierarchy
3. **Provide in-depth analysis** of each method category with representative papers, evolution trajectories, and strengths/limitations
4. **Conduct cross-method comparison** with tables and qualitative trade-off analysis
5. **Identify research trends and open problems** grounded in actual publication data
6. **Generate a professionally typeset PDF report** (10,000-25,000 words) via the included Markdown-to-PDF converter

## Installation

### Option 1: Install via Skill Directory

Copy the skill into your Claude Code skills directory:

```bash
# Clone the repo
git clone https://github.com/linwenye/Literature-Review-Skill.git

# Copy to Claude Code skills directory
cp -r Literature-Review-Skill ~/.claude/skills/literature-review
```

### Option 2: Install as Project Skill

Place the skill in your project's `.claude/skills/` directory:

```bash
cd your-project
mkdir -p .claude/skills
cp -r /path/to/Literature-Review-Skill .claude/skills/literature-review
```

### Dependencies

The PDF generation requires Python packages:

```bash
pip install weasyprint markdown
```

## Usage

In Claude Code, simply describe what you want to survey:

```
帮我 survey 一下 multi-agent collaboration 的论文
```

```
Do a literature review on retrieval-augmented generation
```

```
帮我梳理一下 LLM reasoning 这个方向的 related work
```

The skill triggers automatically on phrases like: `paper survey`, `literature review`, `related work`, `survey this area`, `论文调研`, `文献综述`, etc.

## Output

The skill produces:
- A Markdown report (`[topic]_paper_survey.md`)
- A professionally typeset PDF (`[topic]_paper_survey.pdf`) with cover page, headers, page numbers, and styled tables

## Languages

- `SKILL.md` -- Chinese version (original)
- `SKILL_EN.md` -- English version

Both versions are functionally equivalent. Claude Code will use whichever is configured as the skill entry point.

## Project Structure

```
.
├── SKILL.md              # Skill definition (Chinese)
├── SKILL_EN.md           # Skill definition (English)
├── scripts/
│   └── md_to_pdf.py      # Markdown → PDF converter (WeasyPrint)
├── references/
│   └── schema.json        # Structured survey schema
└── README.md
```

## Acknowledgements

This skill is inspired by [khazix-skills](https://github.com/KKKKhazix/khazix-skills) -- an excellent collection of Claude Code skills for content creation and research. We built upon that foundation to create an **academic-focused variant** specifically tailored for systematic literature reviews, paper surveys, and related work analysis.
