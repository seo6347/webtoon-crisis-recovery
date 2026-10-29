# Environment and execution

## Reproduction scope

Use Python 3.12 and the prepared data archive. Start at **Step 5. 데이터 수집 검증 코드**, including its initial imports/path setup cell, then execute every code cell below it in order through Step 9. Do not use Run All from the top of the notebook: Steps 1–4 document historical collection and cleaning.

The analysis uses collected data and finalized comment labels. It does not repeat live scraping, LLM labeling, or human review, and requires no LLM API key.

## Prepare the data

Extract the prepared archive at the repository root. The result must be `data/processed/` and `data/팀원별 라벨링/`, not `data/data/processed/`.

Required inputs include six processed Parquet tables, the per-title comments under `data/processed/comments/`, `data/processed/labeling_result_codex_final.xlsx`, and the four final rater workbooks under `data/팀원별 라벨링/`. Generated `rq1_*`–`rq4_*` files and plots do not need to be downloaded beforehand.

Download the verified archive and follow the directory and checksum instructions in [Prepared Data](data.md). The linked archive matches the data used in the successful analysis execution check.

## Option A: uv

From the repository root:

```bash
uv sync --locked --group dev
uv run --locked --group dev python -m ipykernel install --user --name webtoon-crisis-recovery --display-name "Python (webtoon-crisis-recovery)"
uv run --locked --group dev jupyter lab webtoon_crisis_recovery.ipynb
```

Select **Python (webtoon-crisis-recovery)** as the notebook kernel. In VS Code, open the notebook and select the same kernel. The working directory must be the repository root, so relative `data/` paths resolve correctly.

`pyproject.toml` pins the eight direct analysis dependencies used in the execution check. `uv.lock` records the resolved dependency graph; the original check was not performed with this newly generated lockfile.

## Option B: venv and pip

Create a Python 3.12 environment:

```bash
python3.12 -m venv .venv
```

On Windows, use `py -3.12 -m venv .venv` if appropriate. Activate it:

```bash
# macOS / Linux
source .venv/bin/activate
```

```powershell
# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Install dependencies from `pyproject.toml` using Python 3.12's built-in TOML reader:

```bash
python -c "import subprocess,sys,tomllib; p=tomllib.load(open('pyproject.toml','rb')); subprocess.check_call([sys.executable,'-m','pip','install',*p['project']['dependencies'],*p['dependency-groups']['dev']])"
python -m ipykernel install --user --name webtoon-crisis-recovery --display-name "Python (webtoon-crisis-recovery)"
python -m jupyterlab webtoon_crisis_recovery.ipynb
```

This installs pinned direct analysis dependencies, but does not apply the uv lockfile to transitive dependencies.

## Fonts

Some plotting cells select `Malgun Gothic`. It must be available to Matplotlib for those Korean labels to render correctly. For other operating systems, install a Korean font and update the affected font settings to that installed font. Font warnings do not imply a data-processing failure; matching figure appearance requires a separate font check.

## Validation record

On 2026-10-29, a fresh Python 3.12.14 process executed all 26 code cells from Step 5 to the final RQ4 cell using the uploaded prepared archive. All cells completed. The data-validation report contained zero errors and warnings, and primary-cause Krippendorff's alpha was 0.936. This check covered analysis and saved figure generation with a non-interactive backend, not identical visual rendering or independent verification of every project claim.

## Historical collection code

Optional collection packages can be installed with `uv sync --locked --group dev --extra collection`. This only installs the optional packages: some historical collection helpers reference external modules and are not a supported end-to-end recollection workflow.
