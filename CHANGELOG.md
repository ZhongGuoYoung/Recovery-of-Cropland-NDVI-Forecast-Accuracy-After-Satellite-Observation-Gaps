# Archive v2 — changes and open items

Basis: `ConvGRU_NDVI_代码数据包_20261118.zip` (102.57 MB, 505 entries, 429.4 MB
unpacked). Every file from v1 is carried over byte-identically; only the items
below were added or replaced.

## Added or replaced

| Item | Change |
| --- | --- |
| `environment/requirements.txt` | Added `torch==2.11.0` and `lightgbm>=4.0`; documented the CUDA 12.6 / CPU wheel indexes in comments. The 14 original pins are unchanged. |
| `LICENSE` | New. MIT for code, frozen weights, window index and derived tables; upstream data stay under their own terms. |
| `CITATION.cff` | New. Software metadata plus a preferred citation to the letter. ORCID fields left out until all authors have registered. |
| `README.md` | Rewritten in English (scope, contents, verification, environment, running, rebuild chain, figures, limitations, license). |
| `README.zh.md` | The v1 Chinese README, unchanged, kept as the Chinese version. |
| `figures/` | New. `make_fig4.py`, `make_figs3_study_area.py`, `plot_fig4_sealed_case.py`, `us_states.json`, `us_counties_fips.json`, `fig4_source_panels.png`, `talor.png`, and a usage README with the exact path edits. |
| `data/Paper300_county_roster.csv` | New. The 300-county roster, needed by both figure scripts. |
| `SHA256SUMS` | Regenerated over the v2 tree (all files except `SHA256SUMS` itself). |

## Still open

1. `project/cropclimatex_delta_transition_experiment.py` is imported by
   `cropclimatex_multicounty/common.py`, `h8_common.py`,
   `evaluate_h8_assimilation_3a.py`, `paper300_formal_experiment/src/paper300_common.py`,
   `scripts/01_freeze_paper300_experiment.py` and
   `scripts/06_train_evaluate_paper300_h8.py`, but the file is in neither this
   archive nor the local project folder. It must be recovered before the
   preparation/training chain (steps 01-06) can be re-run. It is not part of the
   35 frozen code entries and does not affect the sealed replay or the hash audit.
2. The prepared arrays (about 11 GB train/development, 3.6 GB sealed), raw
   remote-sensing and weather archives, and training breakpoints stay on the
   project machine by design.
3. The plotting script for Fig. 2 and Fig. S1 was not kept; their numbers are in
   `work/ndvi_recovery_sealed_v2/reports/recovery_curves.csv`.
4. `lightgbm` has no recorded version; the pin is `>=4.0` until the original
   environment can be queried.
5. Author ORCIDs are not yet in `CITATION.cff`; add them once available.
