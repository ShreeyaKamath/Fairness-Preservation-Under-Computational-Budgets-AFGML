# Requirement → evidence matrix

Status key: **complete** = run on the real UCI file (5 seeds, `results/`, SHA-256 in `data/README.md`) with an artifact; **partial/limitation** noted per row. `results/smoke/` is a synthetic fixture run and is not evidence. Run provenance: `results/manifest.csv`, `results/summary.json` (24/24 automated checks PASS), 93 unit tests pass. Real-run key findings below are the headline numbers; full tables are in `results/metrics/`.

| ID | Requirement | Config & command | Artifact / key output | Status | Report/slide |
|---|---|---|---|---|---|
| C-GM | VE vs clique tree within 1e-6 | `configs/evaluation.yaml`; `run_experiments.py` (Exp 1) | `metrics/exp1_exact_agreement.json` | complete: max error 2.2e-16 (tol 1e-6), natural and masked evidence | §3, S5 |
| C-STAT | Paired uncertainty, valid unit | `evaluate.py` | `metrics/bootstrap.csv` (block bootstrap, 1,000 reps) | complete: 3 confirmatory comparisons, Holm; block bootstrap (rows are not distinct customers, so intervals may be too narrow) | §3–4 |
| C-LEAK | No post-contact variable | `configs/data.yaml`; `tests/test_data.py`, `test_bn.py` | tests pass; `assert_no_leakage` | complete | S4 |
| C-SPLIT | Positional split, train-only fitting | `prepare_data.py` | `data/processed/*` | complete | S4 |
| C-SEEDS | ≥ 5 seeds for stochastic parts | `configs/main.yaml: seeds` | `main_results.csv`, `seed_aggregate.csv` | complete | S7 |
| C-SMALL | Group sizes, denominators, < 30 flagged | `prepare_data.py`, `run_experiments.py` | `group_denominators.csv`, `group_table.csv` | complete | S4, S8 |
| C-SEL | Preregistered primary metrics, validation selection | `docs/protocol.md` | `chosen_fallbacks.csv` | complete | S6 |
| C-MULT | Holm for confirmatory family | `evaluate.py` | `bootstrap.csv: p_holm` | complete | S8 |
| C-SIM | Simulation labelled | all synthetic outputs under `exp5_*`, `*dependent*` | banners/labels | complete | all |
| P9-E1 | Exact posteriors agree across orders & clique tree | Exp 1 | `exp1_exact_agreement.json` | complete | S5 |
| P9-E2 | Preprocessing/query cost, peak factor, memory by workload | Exp 2 | `exp2_complexity.csv`, `fig_complexity.png` | complete | S11 |
| P9-E3 | Controlled budgets: error, fallback rate, decisions by group | `run_core` sweep | `main_results.csv`, `group_table.csv`, budget-curve figs | complete | S8 |
| P9-E4 | Proposed fallback vs equal-budget alternatives at matched utility | `run_core` (methods) | `bootstrap.csv`, `seed_aggregate.csv` | complete | S8 |
| P9-E5 | Synthetic treewidth × availability incl. no-group-dependence control | Exp 5 | `exp5_synthetic.csv`, `exp5_aggregate.csv`, `fig_synthetic_mechanism.png` | complete | S9 |
| P9-A1 | Remove fairness-aware fallback selection | method `util_sel` | `main_results.csv` | complete | S9 |
| P9-A2 | One global fallback | method `global_prior` | `main_results.csv` | complete | S9 |
| P9-A3 | Disable evidence-based simplification | tag `ablation_no_simplification` | `main_results.csv` | complete | S9 |
| P9-A4 | Generous budgets ≈ unrestricted | budget level q = 1.00 | `main_results.csv` | complete | S9 |
| P9-R1 | Different elimination orders | tags `robust_heuristic_*` | `main_results.csv` | complete | S9 |
| P9-R2 | Network densities | tags `robust_structure_{sparse,dense}` | `main_results.csv` | complete | S9 |
| P9-R3 | Discretisations | tag `robust_discretization` | `main_results.csv` | complete | S9 |
| P9-R4 | Operation budgets | q ∈ {.5,.75,.9,.95,1} | `main_results.csv` | complete | S8 |
| P9-R5 | Evidence masks | workloads natural/dependent/independent | `main_results.csv` | complete | S8 |
| P9-R6 | Decision thresholds | tags `robust_capacity_*` | `main_results.csv` | complete | S9 |
| P9-R7 | Wall-clock separately, repeated batches | `profile_latency.py` | `metrics/latency.csv` | complete | S12 |
| P9-B | Predictive baselines (LR, GBT); fairness baselines | `run_experiments.py` | `predictive_baselines.csv` | complete | S7 |
| D-1…7 | Proposal, related work, cards, assumptions, repo, report, demo | `docs/`, `report/`, `slides/`, `demo/` | files present | complete except that related-work reading and the proposal freeze date are for the students to confirm | — |
| P9-RT | Group-unaware routing comparison (brief: if protected attributes are used in routing) | `run_core` method `proposed_unaware` | `main_results.csv`, `bootstrap.csv` (exploratory) | complete (exploratory) | S8 |
| P9-IG | Elimination traces, induced cliques, max clique size, evidence propagation | `src/diagnostics.py`; notebook §4 | notebook output | complete | S5 |
| P9-S2 | Secondary questions: largest factors by evidence pattern; errors near the threshold | `evidence_pattern_costs`, `error_vs_threshold`; notebook §6, §12 | notebook output | complete | S11 |
| P9-E5b | Treewidth × availability-gap factorial (SIMULATED) | notebook §13 | `metrics/exp5b_factorial.csv` | complete | S9 |

## Key findings per requirement (real run, natural workload, q = 0.75, budget = 11 ops, capacity 20 %, 5 seeds)
- **C-GM / P9-E1:** max error 2.2e-16 (VE vs clique tree), 1.1e-16 (vs enumeration); tolerance 1e-6.
- **P9-E4 / C-STAT (paired ΔEOpp, proposed − comparator, 95 % block-bootstrap interval, Holm p):** global fallback +0.120 [+0.015, +0.226] (p = 0.036, **refutes** the hypothesis); utility-only −0.294 [−0.370, −0.210] (p = 0.006, supports); threshold adjustment +0.106 [−0.006, +0.251] (p = 0.080, inconclusive).
- **Utility:** proposed AP 0.421 (exact 0.421); global fallback AP 0.350 (loss 0.071, far above the 0.01 tolerance), utility-only 0.354.
- **P9-E5 (simulated):** group-dependent availability changes the fallback-rate gap by +0.516 versus 0.026 in the group-independent control.
- **P9-A3 (no evidence-based simplification):** the proposed rule collapses to the age-prior fallback (AP 0.356, ΔEOpp 0.722) — the ablation that most weakens the mechanism.
- **Failure case (P9-R2, sparse structure):** the group-aware proposed rule gives AP 0.397 and ΔEOpp 0.794, while the group-unaware routing gives AP 0.460 and ΔEOpp 0.535.

**Report/slide locations:** report sections 3–4 (`report/AFGML_Report.pdf`); slides 5–9 and backups 11–13 (`slides/AFGML_Presentation.pdf`). Experiment-producing commit: `e2350bcd6d58361ddf3e4e7adcdc5d8383f586c2`.
