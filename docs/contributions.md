# Contributions

**Team:** Shreeya Kamath (roll no. 261090830022) and Misha (roll no. 261090830020) · ICT 5143 AFGML, Problem 9.
Commit count is not a proxy for contribution. Both students can explain the whole pipeline: the question, the Bayesian network, the fairness choice, validation, main findings and limitations.

## Contribution table

| Work item | Lead | Support / reviewer | Artifact / commit | What was verified |
|---|---|---|---|---|
| Factor operations, VE, clique tree, C-GM | Shreeya | Misha | `src/inference.py`, `tests/test_cgm.py` | VE (3 heuristics) = clique tree = enumeration; max error 2.2e-16 vs tolerance 1e-6 (`results/metrics/exp1_exact_agreement.json`); tests pass |
| Data preparation, leakage guards | Shreeya | Misha | `src/data.py`, `scripts/prepare_data.py`, `tests/test_data.py` | `duration` absent from every split; real file matches published UCI counts (41,188 x 21, 4,640 yes); SHA-256 recorded |
| BN fitting, budgeted inference, fallbacks | Shreeya | Misha | `src/bn.py`, `src/fallbacks.py`, `src/diagnostics.py` | Operation-count budget deterministic (11 ops at q=0.75; 258 ops at q=1.0); fallback rate 20.1% of natural-workload queries |
| Metrics, bootstrap, Holm | Shreeya | Misha | `src/metrics.py`, `scripts/evaluate.py`, `tests/test_metrics.py` | Paired block bootstrap, 1,000 replicates, block length 50, Holm over 3 comparisons (`results/metrics/bootstrap.csv`) |
| Experiments, synthetic study, figures | Shreeya | Misha | `scripts/run_experiments.py`, `src/synth.py`, `scripts/make_results.py`, `notebooks/AFGML_P9_Project.ipynb` | 5 seeds; Exp 1–5 and ablations/robustness rows present in `results/metrics/main_results.csv` |
| Debugging and test-suite verification | Misha | Shreeya | `tests/`, `results/summary.json`, `results/manifest.csv` | 93 tests pass from a repo-root run; 24/24 automated requirement checks PASS in `results/summary.json` |
| Report (2-3 pages, source and PDF) | Misha | Shreeya | `report/AFGML_Report.tex`, `report/AFGML_Report.pdf` | Every number comes from `results/tables/macros.tex` and `main_table.tex`, not typed by hand; compiled twice with no errors |
| Slides: content | Misha | Shreeya | `slides/AFGML_Presentation.tex` | Results/claims on slides match the report and `results/` |
| Slides: alignment and layout | Shreeya | Misha | `slides/AFGML_Presentation.tex`, `slides/AFGML_Presentation.pdf` | Compiled Beamer PDF checked frame by frame for overflow and figure/table placement |
| Related work, protocol, cards, requirement matrix | Misha | Shreeya | `docs/` | Requirement matrix rows mapped to real artifacts (see `docs/requirements_matrix.md`) |

**Shreeya Kamath** owns the implementation (inference engine, data pipeline, BN and fallback logic, metrics, experiments) and the alignment/layout of the presentation.
**Misha** owns debugging and verification, the report, and the presentation content.

## Individual statements

**Shreeya Kamath (261090830022).**
*Technical decision:* I defined the compute budget as a deterministic count of factor-entry operations rather than wall-clock time, so a query "trips" the budget for a reproducible, evidence-dependent reason (the pattern of `unknown` values) and results do not depend on the machine. Wall-clock is profiled separately (`results/metrics/latency.csv`).
*Difficulty/failure:* The central hypothesis failed against the global fallback: the paired difference in ΔEOpp is +0.120 [+0.015, +0.226], so our method is *worse* on that metric. The global fallback lowers ΔEOpp mainly by lowering the true-positive rate of every group (AP falls from 0.421 to 0.350, far beyond the 0.01 tolerance), so it is not a matched-utility win.
*Cross-check of Misha's work:* I checked that the report and slide numbers match `results/tables/macros.tex` and `results/metrics/bootstrap.csv`, and that the report states the refutation rather than a positive claim.

**Misha (261090830020).**
*Technical decision:* I treated the Holm-adjusted paired interval as the only basis for a claim, exactly as pre-registered in `docs/proposal.md`; I reported the result as mixed (supported against utility-only selection, refuted against the global fallback, inconclusive against threshold adjustment) instead of selecting the favourable comparison.
*Difficulty/failure:* Debugging the evidence-handling path: group-level estimates rest on very small groups (several age-band x marital cells have fewer than 30 positives), so I made sure the primary ΔEOpp uses only the 7 supported groups and flagged the rest as exploratory (the all-groups sensitivity interval is very wide).
*Cross-check of Shreeya's work:* I re-ran the test suite (93 tests pass) and compared VE, clique-tree and enumeration posteriors in `exp1_exact_agreement.json` (max error 2.2e-16 vs tolerance 1e-6), and checked that `duration` is excluded from every split.

*Disclosure: the initial scaffold of code and documents was produced with an AI assistant (Claude). Follow your course policy on disclosure; both students must be able to explain and modify every part.*
