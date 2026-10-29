# Prepared Data

## Download

[Download data.zip from Google Drive](https://drive.google.com/file/d/15eoIuDqCiX1QvS9BnkfTSSbzZDW5uYbA/view)

The archive contains collected/processed webtoon data, finalized comment labels, and rater workbooks used by the notebook. On 2026-10-29, the Drive archive was downloaded and its SHA-256 matched the uploaded archive used in the successful analysis execution check. Drive permission metadata showed link-based `anyone / reader` access with discovery disabled; an anonymous-browser download was not separately tested.

| Item | Value |
| --- | --- |
| Archive size | 79,066,021 bytes (79.1 MB / 75.4 MiB) |
| Files | 711 |
| Collected works | 694 |
| Episode rows | 59,991 |
| Comment files / rows | 694 / 721,996 |
| Finalized label rows | 46,815 |

## Integrity and extraction

Save the archive as `data.zip` at the repository root. If the browser adds a suffix, rename the downloaded copy before running these commands.

Optional SHA-256 check, using Python 3.12:

```bash
python -c "import hashlib; from pathlib import Path; actual=hashlib.sha256(Path('data.zip').read_bytes()).hexdigest(); expected=Path('docs/data.sha256').read_text().split()[0]; print(actual); assert actual == expected, 'Archive checksum mismatch'"
```

Expected SHA-256:

```text
ec0974b1ddb34def6d9785f5f323b5ee96083af6e90f1cca517a2dd6e80295eb
```

Extract at the repository root:

```bash
python -m zipfile -e data.zip .
```

The ZIP already contains `data/`. Do not extract it into an additional `data/` folder.

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

## Validate and analyze

Follow [Environment Setup](setup.md), restart the kernel, and start at **Step 5. 데이터 수집 검증 코드**, including the initial imports and path setup. Continue through Step 9 in order.

Step 5 generates `data/logs/validation_report.json` and `validation_issues.csv`. Inspect errors, warnings, and dataset row counts before proceeding. In the successful execution check, all validation issues were zero.

The subsequent `rq1_*`–`rq4_*` tables and plots are generated outputs and do not need to be supplied separately.

## Scope

The archive supports replaying analysis from collected data and finalized labels. It does not rerun live collection, LLM labeling, or human review. No LLM API key is required.

Downloaded inputs, archives, and generated outputs are excluded from Git. Keep the archive unchanged if using the recorded checksum. Replacing it requires updating the checksum and checking compatibility again.

Underlying webtoon metadata and reader comments originate from Naver Webtoon; labels were prepared by the project team. Public download access is not a separate license grant for third-party content.
