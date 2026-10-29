# Webtoon Crisis and Recovery Analysis

**How do webtoons recover after a decline in reader ratings, and how does recovery differ across the causes expressed in reader comments?**

A four-person Data Science course project at **Soongsil University**, combining episode-rating time series with reader best comments to study crisis detection, recovery strength, persistence, and subsequent crises.

**Team:** 쉬었음청년 · **Team lead / repository maintainer:** [Donggyun Seo](https://github.com/seo6347)

**My contributions:** RQ3 recovery/transition analysis, RQ4 work-level trajectory analysis, and data collection and preprocessing.

[Analysis Notebook](webtoon_crisis_recovery.ipynb) · [Download Prepared Data](https://drive.google.com/file/d/15eoIuDqCiX1QvS9BnkfTSSbzZDW5uYbA/view) · [Data Guide](docs/data.md)

## Project Overview

The project links changes in episode ratings to the themes expressed in reader comments. Rather than treating a single rating rebound as complete recovery, the workflow examines crisis intensity, recovery strength, how long recovery lasts, and the direction of the next crisis.

The integrated notebook includes the original collection and cleaning code for reference, followed by the executable analysis workflow.

| Stage | Question / purpose | Main outputs |
| --- | --- | --- |
| **RQ1 — Crisis detection** | Where do rating declines occur, how severe are they, and how can adjacent crisis episodes be grouped? | Crisis episodes, severity measures, crisis clusters |
| **RQ2 — Comment causes** | What causes and sentiments appear in comments on crisis episodes? | Comment extraction, labeling inputs, finalized labels, inter-rater agreement |
| **RQ3 — Recovery and transitions** | How do recovery strength, persistence, and subsequent crises differ across causes? | Recovery/transition metrics and cause-level summaries |
| **RQ4 — Work-level trajectories** | What crisis–recovery patterns emerge across a work's episode history? | Work-level trajectory features, typology, representative cases |

## Data and Analysis Scale

The collection snapshot is **May 23, 2026**. The collection covered all collectable works listed across Naver Webtoon's weekday pages at that time, their accessible episodes and episode ratings, and the **best comments for each accessible episode**.

**Completed-series titles were excluded:** they were not accessible through the project's collection workflow. This is a snapshot of the weekday-listed works available for collection, not a corpus of all webtoons ever published on Naver Webtoon. Best comments are a platform-selected subset of reader responses, not all comments.

| Item | Count |
| --- | ---: |
| Collected works | **694** |
| Processed episode rows | **59,991** |
| Reader best-comment rows | **721,996** |
| Finalized comment-label rows | **46,815** |
| Detected crisis episodes | **4,702** |
| Crisis clusters | **1,617** |
| Works in the final RQ4 typology table | **377** |

These counts refer to different stages of the workflow. The 694 collected works are not all included in every downstream analysis; filtering, crisis presence, and follow-up availability determine the relevant analysis samples.

The prepared data archive is **79,066,021 bytes** (about 79.1 MB) and contains 711 files. It is distributed separately from the code. See [Prepared Data](docs/data.md) for its directory layout and input requirements.

## Method

### Crisis detection and grouping

RQ1 identifies low or declining episode ratings using a five-episode moving average and absolute rating thresholds. The detection rules distinguish declines at ratings of at least 9.5, declines below 9.5, and ratings below 9.0. Short interruptions are handled to avoid interpreting a one-episode rebound as sustained recovery.

The workflow excludes works with at most 12 weeks' worth of episodes according to their weekly release frequency. It computes crisis severity and groups crisis episodes, with additional structural splitting for long clusters.

### Comment labeling

RQ2 extracts best comments for detected crisis episodes and prepares cause and sentiment labels. The notebook documents the labeling prompt, including multi-label causes, a primary cause, sentiment, and human-review fields.

Examples of cause labels include story pacing, plot logic, character, art style, production quality, and external trust issues. The released analysis reads the **finalized labeling workbook**; it does not call an LLM to regenerate those labels.

Inter-rater agreement is calculated from the four team members' final labeling workbooks:

| Label dimension | Krippendorff's alpha |
| --- | ---: |
| **Primary cause** | **0.936** |
| Sentiment | 0.828 |

These values measure agreement among human raters on the labeled evaluation material; they are not model accuracy scores.

### Recovery, persistence, and trajectories

RQ3 combines crisis clusters with cause labels to analyze rating rebound, closeness to the pre-crisis baseline, recovery persistence, and the direction of subsequent crises. RQ4 aggregates those results into work-level trajectory features and a rule-based typology.

These are operational definitions implemented in the notebook. They describe observed rating/comment patterns and do not establish that a comment theme caused a particular recovery outcome.

## Selected Descriptive Results

The RQ3 analysis shows differences in strong-recovery rates across dominant substantive causes:

| Dominant substantive cause | Eligible crisis clusters | Strong-recovery ratio |
| --- | ---: | ---: |
| Story pacing | 265 | 80.0% |
| Production quality | 185 | 77.8% |
| Art style | 106 | 55.7% |

Source: the cause-level recovery summary generated by RQ3 at `data/plots/RQ3_analysis/rq3_step5_detail_label_recovery_summary.csv`.

This table groups the notebook's `main_recovery_analysis_sample` by `top_substantive_detail_label`. Strong recovery means `recovery_strength_score >= 0.75`; it is separate from sustained recovery. Counts are crisis clusters, not independent works or comment counts.

The comparison is descriptive and unadjusted. Multiple clusters can belong to the same work, and the table does not establish causal effects or statistical significance.

## Run the Analysis

### 1. Prepare the data

[Download data.zip](https://drive.google.com/file/d/15eoIuDqCiX1QvS9BnkfTSSbzZDW5uYbA/view), save it at the repository root, and extract it there:

```bash
python -m zipfile -e data.zip .
```

The ZIP already contains a top-level `data/` directory. Confirm that `data/processed/` and `data/팀원별 라벨링/` exist directly under the repository root. See the [data guide](docs/data.md) for the required files.

### 2. Set up Python 3.12

With uv installed, run from the repository root:

```bash
uv sync --locked --group dev
uv run --locked --group dev python -m ipykernel install --user --name webtoon-crisis-recovery --display-name "Python (webtoon-crisis-recovery)"
uv run --locked --group dev jupyter lab webtoon_crisis_recovery.ipynb
```

Select **Python (webtoon-crisis-recovery)** as the kernel. In VS Code, open the notebook and select the same kernel. Run from the repository root so relative `data/` paths resolve correctly.

Some plotting cells use `Malgun Gothic` for Korean labels. If it is unavailable, install a Korean font and update the plotting font settings to that font. Font warnings can affect text rendering without indicating a data-processing failure.

### 3. Execute Step 5 through Step 9

Start at **Step 5. 데이터 수집 검증 코드**, including its initial imports/path setup cell, then execute all subsequent code cells in order through the last RQ4 cell.

**Do not run all cells from the top.** Steps 1–4 document historical collection/cleaning and are not required for prepared-data analysis. The working directory must be the repository root.

Outputs are written to `data/processed/rq1_*` through `rq4_*`, `data/logs/`, and `data/plots/`. Downloaded inputs and generated outputs are excluded from Git.

## Repository Guide

| Path | Contents |
| --- | --- |
| [webtoon_crisis_recovery.ipynb](webtoon_crisis_recovery.ipynb) | Integrated submitted collection, cleaning, labeling documentation, and RQ1–RQ4 analysis |
| [pyproject.toml](pyproject.toml) · [uv.lock](uv.lock) | Environment definition and resolved dependencies |
| [docs/data.md](docs/data.md) | Prepared-data download and required input files |
| `data/` (download separately) | Prepared inputs and locally generated analysis outputs |

## Team and Project Context

**Team lead:** Donggyun Seo (서동균)  
**Team members:** 이정원, 한승원, 홍서진

### Donggyun Seo's contributions

- **RQ3:** recovery strength, persistence, and subsequent-crisis transition analysis.
- **RQ4:** work-level crisis–recovery trajectory analysis and typology.
- **Data collection and preprocessing:** contributed to the collection and preparation of the webtoon data used in the integrated workflow.

Data collection was shared across all four team members. The contributions above describe Donggyun Seo's work within the team project; RQ1–RQ4 are presented together to preserve the complete submitted workflow. This repository is maintained by Donggyun Seo.

The earlier [DS_2026_Smeargle repository](https://github.com/PKTOSE/DS_2026_Smeargle) contains part of the collection work. This repository presents the integrated analysis rather than only that collection module.

## Data Source and Course Use

The data were collected and used solely for a university Data Science course project. The scraping/crawling was **not officially authorized by Naver Webtoon**, and this project is not affiliated with or endorsed by Naver Webtoon. This purpose statement does not grant permission to reuse or redistribute third-party content.

## Scope and Limitations

- The dataset covers the collectable weekday-listed works in the May 23, 2026 snapshot; completed-series titles are excluded. Findings should not be generalized to all past or completed webtoons.
- Data consist of collected Naver Webtoon ratings and **best comments**, not a complete or representative sample of every reader response.
- Results depend on the observation window, operational crisis/recovery definitions, label taxonomy, and available follow-up.
- Labeling and typology involve project-defined rules and human judgment.
- Prepared-data reproduction does not repeat live scraping, LLM labeling, or human review; no LLM API key is needed for Step 5–9.
- Naver Webtoon is the source of the underlying metadata and comments. Team-created annotations and public download access do not imply a separate license grant for third-party content.
