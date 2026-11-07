# Data

- **Dataset:** UCI Bank Marketing (dataset 222), file `bank-additional-full.csv`, from https://archive.ics.uci.edu/dataset/222/bank+marketing
- **Acquire:** `python scripts/acquire_data.py` (prints the SHA-256; paste it below). Raw data is git-ignored and must not be redistributed.
- **Expected shape:** 41,188 rows, semicolon-separated, 21 columns incl. `y` — verify after download.
- **Access date / SHA-256:** 2026-11-07 / `74adfc578bf77a7ff4bb1ba4a9f8709d9e3c6907342959c2c8416847e0afb4d8` (41,188 rows x 21 columns, 4,640 `yes`; matches the published UCI counts; recorded in `results/logs/data_provenance.json`). License: CC BY 4.0 (UCI page).
- **Exclusions & preprocessing:** see `docs/data_card.md` and `configs/data.yaml` (`duration` dropped; positional 60/20/20 split; fixed bins).
- **Processed outputs** (`data/processed/{train,val,test}.csv`) are derived data: share only if licence/privacy allow.
