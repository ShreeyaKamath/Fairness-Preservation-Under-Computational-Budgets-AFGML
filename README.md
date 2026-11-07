# Fairness Preservation under Computational Budgets in Probabilistic Decision Systems
**ICT 5143 AFGML Capstone — Problem 9** · Team: **Shreeya Kamath (261090830022)**, **Misha (261090830020)** · UCI Bank Marketing (`bank-additional-full.csv`)

> **Status.** Real-data results (UCI file, SHA-256 in `data/README.md`, 5 seeds) are in `results/`. `results/smoke/` is a synthetic fixture run, kept only to demonstrate file formats; it is **not evidence**.

## 1. Identity and task
`Y = 1`: recorded term-deposit subscription. `D = 1`: selected for *hypothetical* pre-contact outreach (top 20 % by posterior). Decision time = before the call → `duration` and every post-contact variable are excluded (enforced by `assert_no_leakage` + tests).

## 2. Question and contribution
*Can a budget-aware fallback policy reduce the fairness degradation caused by interrupted exact inference at a fixed operation budget?* Closest method: compute-budget → group-disparity work (Das et al., ICML 2024) — conceptually; reproduced baselines are exact inference, Hardt-style post-processing after budgeted inference, and a single global fallback (`docs/related_work.md`). Contribution: a deterministic operation-count budget with evidence-dependent trips, a simulation isolating *group-dependent evidence availability* as a mechanism (with a no-dependence control), and fairness-aware fallback selection. A negative result is acceptable and will be reported as such.

## 3. Model and fairness
9-variable discrete BN (age band, job, education, default, housing, loan, pdays bin, previous bin, poutcome → y; `configs/model.yaml`); exact VE (min-fill / min-weight / min-degree) and clique-tree calibration, both implemented from scratch with factor-entry operation counters (`src/inference.py`). Primary fairness criterion: **ΔEOpp** across age band × marital status; diagnostics ΔDP, ΔEOdds, fallback-rate gaps, decision flips vs exact. Primary utility AP, allowed loss 0.01.

## 4. Results
Natural workload, test n = 8,238, budget = 11 operations (75th percentile of validation costs), capacity 20 %, 5 seeds. Paired difference in ΔEOpp (proposed − comparator; negative favours proposed), 95 % moving-block bootstrap interval (1,000 replicates, block 50), Holm-adjusted over 3 comparisons:

| Comparator | Effect | 95 % interval | Holm p | Reading |
|---|---|---|---|---|
| Global fallback | +0.120 | [+0.015, +0.226] | 0.036 | hypothesis refuted |
| Utility-only selection | −0.294 | [−0.370, −0.210] | 0.006 | supported |
| Threshold adjustment after budget | +0.106 | [−0.006, +0.251] | 0.080 | inconclusive |

| Method | AP | ΔEOpp | Log loss / Brier |
|---|---|---|---|
| Exact (reference) | 0.421 | 0.465 | 0.709 / 0.223 |
| Global fallback | 0.350 | 0.303 | 0.852 / 0.260 |
| Proposed | 0.421 | 0.423 | 0.710 / 0.223 |
| Utility-only | 0.354 | 0.717 | 0.825 / 0.254 |
| Threshold adj. | 0.350 | 0.317 | 0.852 / 0.260 |

**Interpretation.** The proposed rule keeps exact-level AP (loss < 0.001), but the global fallback has a smaller ΔEOpp because it lowers every group's selection quality (AP loss 0.071, above the 0.01 tolerance); it is not a matched-utility improvement. The mechanism result is simulated: group-dependent evidence availability changes the fallback-rate gap by +0.516 versus 0.026 in the control. **Failure case:** with a sparse network structure the group-aware rule picks the age-prior fallback (AP 0.397, ΔEOpp 0.794) while group-unaware routing does better (AP 0.460, ΔEOpp 0.535). Rows are not distinct customers, so intervals may be too narrow; seven groups meet the 30-positive support threshold and the rest are exploratory. Full results: `results/metrics/`, `results/tables/`, `results/figures/`, `docs/requirements_matrix.md`.

## 5. Installation and data
```bash
python -m venv .venv
source .venv/bin/activate            # Windows PowerShell: .venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python scripts/acquire_data.py       # downloads bank-additional-full.csv, prints SHA-256 (record it in data/README.md)
```
If the download fails (proxy etc.), fetch the file manually from the UCI page and put it in `data/raw/`.

## 6. Reproduction
Smoke (synthetic, ≈ 1 min, proves the plumbing only):
```bash
python -m pytest tests
python scripts/run_experiments.py --mode smoke --quick --out results/demo
python scripts/evaluate.py --out results/demo --n-boot 200
python scripts/make_results.py --out results/demo --mode smoke
```
Real data (headline table first, then everything — commands mirror `docs/requirements_matrix.md`):
```bash
python scripts/prepare_data.py configs/data.yaml        # splits + group denominators
python scripts/verify_inference.py --mode real          # C-GM on random BNs
python scripts/run_experiments.py --mode real           # all experiments (≈ 22 min; 5 seeds everywhere)
python scripts/evaluate.py --out results                # paired block bootstrap, Holm, seed aggregates
python scripts/make_results.py --out results --mode real
python scripts/profile_latency.py --mode real           # wall-clock, separate from op budgets
pdflatex -interaction=nonstopmode -halt-on-error -output-directory report report/AFGML_Report.tex   # run twice
pdflatex -interaction=nonstopmode -halt-on-error -output-directory slides slides/AFGML_Presentation.tex
```
Verified: a fresh `git clone` regenerates `results/tables/main_table.tex` and `results/metrics/bootstrap.csv` byte-for-byte equal from the saved runs with `evaluate.py` + `make_results.py` (≈ 15 s). Not re-run from scratch here: the full 22-minute run and the data download (done by the authors in the notebook; no network here). The smoke run cannot substantiate any real-data claim. Tested: the 93-test suite passes (Linux, Python 3.13) and the real-data run was executed on the genuine UCI file in the project notebook (Python 3.13; run provenance in `results/summary.json` and `results/logs/`). The experiment-producing code commit is `e2350bcd6d58361ddf3e4e7adcdc5d8383f586c2`: the SHA-256 of `src/`, `scripts/` and `configs/` at that commit equals the code stamp in `results/logs/notebook_stamp.json`, and `results/manifest.csv` records it. The runs themselves predate version control. The submission is the git tag `submission`. Full real run took 1,307 s (≈ 22 min; Python 3.13, Linux, see `results/metrics/latency.csv` for the machine string); `evaluate.py` + `make_results.py` alone ≈ 40 s.

**Notebook.** `notebooks/AFGML_P9_Project.ipynb` is a full driver: it locates/validates the real CSV (never falls back to synthetic data), runs the whole pipeline, and reports every Problem 9 item, including elimination traces and induced cliques (`src/diagnostics.py`), the group-unaware routing comparison (`proposed_unaware`) and a factorial synthetic study.

## 7. Evidence
Report: `report/AFGML_Report.pdf` (source `.tex`) · Slides: `slides/AFGML_Presentation.pdf` · Repository: https://github.com/ShreeyaKamath/Fairness-Preservation-and-Computational-Budgets-AFGML · Submission = git tag `submission`.

`docs/` — proposal, related work, data card, model card, assumptions, protocol, requirements matrix, contributions · `report/`, `slides/`, `demo/` · `results/manifest.csv` · `notebooks/AFGML_P9_Project.ipynb`.

## 8. Contributions and attribution
Shreeya Kamath: implementation (inference, data, BN/fallbacks, metrics, experiments) and slide alignment. Misha: debugging/verification, report and slide content. Details, cross-review and individual statements: `docs/contributions.md`. Initial scaffold produced with an AI assistant (Claude), disclosed per course policy. Data: Moro, Rita & Cortez, UCI Bank Marketing.

## Layout
`src/` inference, data, bn, fallbacks, fairness, metrics, pipeline, profiling, synth · `scripts/` entry points · `tests/` · `configs/` frozen settings · `results/` outputs.

## Repository audit and submission checks
See [`docs/REPOSITORY_AUDIT.md`](docs/REPOSITORY_AUDIT.md) for the independently inspected file inventory, the locally executed 93-test check, and items that still require author confirmation or clean-machine replication. The original Problem 9 specification must be checked against the requirement matrix before claiming complete course compliance.
