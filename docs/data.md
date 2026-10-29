# Prepared Data

[Download data.zip from Google Drive](https://drive.google.com/file/d/15eoIuDqCiX1QvS9BnkfTSSbzZDW5uYbA/view)

The archive (about 79.1 MB) contains collected and processed webtoon data, finalized comment labels, and the team members' labeling workbooks. See the [README](../README.md#data-and-analysis-scale) for the collection scope and [course-use statement](../README.md#data-source-and-course-use).

## Placement

Save the archive as `data.zip` at the repository root and extract it there:

```bash
python -m zipfile -e data.zip .
```

The ZIP already contains a top-level `data/` directory. The result must be `data/processed/`, not `data/data/processed/`.

## Required layout

```text
webtoon-crisis-recovery/
├── webtoon_crisis_recovery.ipynb
├── pyproject.toml
├── uv.lock
└── data/
    ├── processed/
    │   ├── works_base.parquet
    │   ├── episodes.parquet
    │   ├── webtoon_info.parquet
    │   ├── webtoon_tags.parquet
    │   ├── webtoon_genres.parquet
    │   ├── collection_status.parquet
    │   ├── labeling_result_codex_final.xlsx
    │   └── comments/
    │       └── {title_id}/
    │           └── webtoon_{title_id}_best_comments.parquet
    ├── 팀원별 라벨링/
    │   ├── groundtruth - 동균 최종.xlsx
    │   ├── groundtruth - 서진 최종.xlsx
    │   ├── groundtruth - 승원 최종.xlsx
    │   └── groundtruth - 정원 최종.xlsx
    └── works_base.csv
```

The archive also contains initial rater workbooks and `groundtruth_merged.xlsx`. The default agreement calculation reads the four final workbooks and regenerates the merged file. `works_base.csv` belongs to historical collection/EDA and is not required by the Step 5–9 path.

## Inputs and generated outputs

Required inputs for Step 5–9 are the six processed Parquet tables, per-title best-comment files, finalized comment-label workbook, and four final rater workbooks shown above.

The analysis generates `rq1_*`–`rq4_*` tables, `data/logs/`, and `data/plots/`. These outputs do not need to be downloaded separately. Downloaded inputs and generated outputs are excluded from Git.

Follow [Run the Analysis](../README.md#run-the-analysis) for environment setup and execution order. The workflow reads prepared data and finalized labels; it does not repeat live collection, LLM labeling, or human review.
