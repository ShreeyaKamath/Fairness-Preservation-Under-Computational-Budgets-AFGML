# Proposal — ICT 5143, Problem 9

**Title.** Fairness Preservation under Computational Budgets in Probabilistic Decision Systems (UCI Bank Marketing).

## Research question
Can a budget-aware fallback policy reduce the fairness degradation caused by interrupted exact inference in a Bayesian-network outreach model, at a fixed deterministic operation budget?

## Decision context
`D = 1`: customer selected for *hypothetical* pre-contact outreach (capacity-limited). `Y = 1`: recorded term-deposit subscription. Stakeholders: customers, outreach teams, serving engineers, auditors. No causal marketing claims.

## One falsifiable contribution sentence
> At a fixed operation budget, choosing the fallback by validation worst-group decision deviation from exact inference (subject to ≤ 0.01 absolute AP loss) yields a smaller ΔEOpp than a single global fallback, utility-only fallback selection, and ordinary threshold adjustment applied after budgeted inference, on the natural evidence workload.

**Refuted if** the Holm-adjusted paired 95 % interval for ΔEOpp(proposed) − ΔEOpp(comparator) contains 0 or is positive for any family member, or if the proposed rule exceeds the AP-loss tolerance on test. A null/negative outcome is a reportable finding.

**Mechanism claim (simulation).** Group-dependent *evidence availability* alone — with exact inference unchanged — produces group differences in budget-triggered fallback rates; a workload with identical availability across groups does not. Exact elimination order never changes the posterior; orders only change which queries exceed the budget.

## Preregistered choices (frozen in `configs/`)
Primary predictive metric AP; allowed AP loss 0.01; primary fairness ΔEOpp over age band × marital; capacity 20 %; primary budget = 75th percentile of natural *validation* exact op counts; confirmatory family `{global_prior, util_sel, thr_adj_post_budget}` with Holm; synthetic-mask workloads are exploratory; seeds 0–4.

## Amendments log
| Date | Change | Type |
|---|---|---|
| 2026-11-07 | First real-data run (UCI file, SHA-256 in `data/README.md`). Whether `configs/` were frozen before any test outcome was inspected is **to be confirmed by the students**; the configs were written with the scaffold, before the real file was available | prior choice (students to confirm) |
| 2026-11-07 | Macro/calendar columns excluded and `unknown` treated as unobserved evidence (`configs/data.yaml`) | prior choice (declared in the scaffold config) |
| 2026-11-07 | Result: hypothesis **refuted** against the global fallback, **inconclusive** against threshold adjustment, supported against utility-only selection. Reported as is; the claim was not narrowed after seeing results | retrospective note |
