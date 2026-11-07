# Repository preparation audit (2026-11-07)

Basis: `AFGML_Repository_Guide.md` and the supplied `afgml-p9_final.zip`. This is an audit of supplied artifacts, **not** an independent replication of the original UCI experiment.

## Confirmed in the supplied archive

- Project-specific README, two students and roll numbers, question, negative headline result, installation and commands.
- `LICENSE`, `.gitignore`, pinned `requirements.txt`, four configs, data acquisition and data documentation.
- Proposal, related work, data/model cards, assumptions, protocol, requirement matrix and contribution statements.
- Importable `src/`, runnable entry-point scripts, notebook, tests, machine-readable results, plots, logs, manifest, report source/PDF, slides source/PDF and demo guide.
- Synthetic smoke results explicitly separated from real-data outputs.
- Test suite: **93 passed** in the current inspection environment using `python -m pytest -q tests` (2026-11-07). This does not verify the full research experiment.

## Items requiring human confirmation before submission

1. **Author/contribution accuracy:** confirm the teammate's preferred full name and both roll numbers; each student should approve the specific ownership and first-person reflection in `docs/contributions.md`.
2. **Original selected-problem specification:** `AFGML_Research_Capstone_Problems.tex` was not provided separately. The requirement matrix reflects the supplied project, but cannot be certified against the authoritative Problem 9 text without that source.
3. **Clean-machine replication:** independently run data acquisition, the complete real-data pipeline, and both LaTeX builds. This inspection ran tests only, not the full 22-minute evaluation or network acquisition.
4. **Environment reproducibility:** `requirements.txt` pins versions but does not include transitive package hashes or an independently generated lock file. Python 3.13/Linux is the documented tested setup; other operating systems require validation.
5. **Sampling limitation:** rows lack customer identifiers, so the moving-block uncertainty intervals cannot guarantee independence at the customer level. Retain this limitation in public claims.
6. **Provenance:** confirm the saved Git commit and `submission` tag match the final pushed repository; the archive contained its own `.git` directory, deliberately excluded from this upload-ready ZIP to avoid overwriting remote Git history.
7. **Data rights:** keep raw and processed customer data out of the repository; confirm dataset attribution and the project's code license with both authors.
8. **Claims:** the method has higher ΔEOpp than global fallback (+0.120, 95% interval [+0.015,+0.226]); do not describe this as fairness improvement. AP matching exact inference does not imply equal fairness.

## Upload checklist

- Review the changes, especially this audit, `README.md`, and contributor statements.
- Upload files into the existing repository root; do not upload the ZIP itself as the repository's sole content.
- Run `python -m pytest -q tests` and at least the synthetic smoke commands in the README.
- For full replication, acquire UCI data locally and follow the real-data commands in the README.
- Check GitHub renders README, report PDF, slide PDF and figures; verify no sensitive data or secrets were committed.
- Only after verification, commit, push, and tag the final submission according to course instructions.
