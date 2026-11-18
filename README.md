# ConvGRU NDVI Recovery — code, frozen weights, window index, and result tables

Reproducibility archive for the IEEE GRSL letter *Recovery of Cropland NDVI
Forecast Accuracy After Satellite Observation Gaps* (Zhong, Chang, Huang, and
Xue). The authoritative record of the sealed main evaluation is
`work/ndvi_recovery_sealed_v2/frozen_plan.json`. The ConvLSTM cross-model replay
and the no-Daymet sealed replay each carry their own plan and are post-unblinding
supplementary analyses. All original experiment files are kept with their
original bytes and relative paths.

> **Download:** the full archive is attached to the [latest release](../../releases/latest) as `ConvGRU_NDVI_reproducibility_archive.zip` (about 101 MB). This repository holds the documentation, license, citation metadata, and release checksum; the code, frozen weights, window index, and tables are inside the ZIP. Verify the downloaded ZIP against `SHA256SUMS.release`.

## Repository layout

| Path | Content |
| --- | --- |
| `README.md` / `README.zh.md` | This documentation, in English and Chinese |
| `LICENSE` | MIT license for code, frozen weights, window index, and derived tables |
| `CITATION.cff` | Citation metadata for the archive and the letter |
| `CHANGELOG.md` | Archive v2 changes and open items |
| `SHA256SUMS.release` | SHA-256 of the release ZIP |
| `Releases` | The full archive (`ConvGRU_NDVI_reproducibility_archive.zip`) |

## What is inside

| Path | Content |
| --- | --- |
| `portable.py`, `run.ps1`, `project/`, `environment/` | Entry point, models, training and replay, data preparation, environment record |
| `checkpoint_inventory.csv` | 36 best checkpoints with variant, seed, best epoch, training-history length, SHA-256, training-plan hash, and a flag marking whether the checkpoint hash is registered in a verified downstream plan |
| `frozen_hash_audit.csv` | File-level audit of the frozen plan and related plans; all 35 code entries of the sealed main plan match |
| `artifacts/paper300/prepared/audited_manifests/` | Audited train and development manifests, hashes matching the frozen plan: 122,553 training H8 windows and 9,452 development windows |
| `artifacts/paper300/frozen/`, `artifacts/paper300/reports/`, `artifacts/paper300/logs/` | Frozen protocol, download manifest, source fingerprints, PID audit, preparation and training logs |
| `window_index/sealed_matched_windows.csv` | 11,315 sealed paired windows, keyed jointly by PID and first release date, with the three forecast origins for the 4-, 8-, and 16-period gaps |
| `window_index/sealed_matched_windows_by_gap.csv` | The same cohort expanded by gap: 33,945 rows |
| `work/ndvi_recovery_sealed_v2/` | Sealed manifest, spatial audit, execution record, twelve seed-replay caches, and metric tables (primary effects, recovery curves, recovery time, paired effects, county-year coverage) |
| `work/ndvi_recovery_weather_v1/`, `work/ndvi_recovery_cross_model_v1/cuda/` | No-Daymet and cross-model plans, replay caches, and summaries |
| `work/*/formal/runs/`, `work/ndvi_recovery_weather_v1/runs/` | Per-epoch training history and the final `run.json` for each run; `.log` files are kept where they existed |
| `figures/` | Plotting scripts and map assets for Fig. 4, Fig. S3, and the sealed-case panel |
| `SHA256SUMS` | Checksums of every file in the archive |
| `LICENSE`, `CITATION.cff`, `README.zh.md` | MIT license, citation metadata, Chinese version of this file |

Cohort sizes: the main cohort covers 89 counties; the sub-cohort with at least
12 follow-up periods holds 6,458 windows over 86 counties. `max_reported_followup`
is 8 or 12, and `available_followup_periods` is the number of follow-up periods
actually available for that window. Import the CSVs with FIPS as text so leading
zeros are preserved.

## Verify the archive

```bash
# Linux / WSL, from the extracted root
sha256sum -c SHA256SUMS

# Any platform, re-checks the immutable files reproduced by the experiment
python portable.py check --full-hash
```

Expected values: 11,315 rows in `sealed_matched_windows.csv`, 33,945 in the
by-gap long form, 122,553 and 9,452 windows in the audited manifests, 36 rows in
`checkpoint_inventory.csv`, and 35 of 35 frozen code entries matching.

## Environment

Python 3.12.13 (conda-forge), PyTorch 2.11.0+cu126, CUDA 12.6. Install PyTorch
from the matching wheel index before the rest:

```bash
# GPU (CUDA 12.6)
pip install torch==2.11.0 --index-url https://download.pytorch.org/whl/cu126
# CPU only
pip install torch==2.11.0 --index-url https://download.pytorch.org/whl/cpu

pip install -r environment/requirements.txt
```

`lightgbm` is intentionally unpinned (`>=4.0`): its exact version was not captured
in `environment/source_environment.json`. `torchvision` is not used anywhere in
this archive.

## Running

On Windows:

```powershell
.\run.ps1 check -FullHash      # integrity check
.\run.ps1 -Action main-evaluate  # replay the sealed main evaluation
```

On Linux or macOS:

```bash
python -u -c 'import portable; from ndvi_recovery_sealed_v2.run import main; main(portable)' freeze
python -u -c 'import portable; from ndvi_recovery_sealed_v2.run import main; main(portable)' run --device cuda
```

Both paths require the migrated shard directories. `portable.py` maps the original
absolute shard locations to `data/shards` and `artifacts/paper300` inside the
extracted bundle; see `relocate()` for the three accepted prefixes.

## Rebuilding the prepared arrays from public inputs

The prepared arrays are not redistributed. They are rebuilt from public data with
the scripts under `project/`:

1. `project/scripts/data/download_cropclimatex_paper300.py` fetches CropClimateX
   (doi: 10.57967/hf/5047), revision
   `3e32ff22ba2ae48c3eb5a4da75c4fbe8eb90f678`; MOD09A1.061, Daymet, and the CDL
   layers come from their original providers.
2. `project/paper300_formal_experiment/scripts/01_freeze_paper300_experiment.py`
3. `.../02_prepare_paper300_h8.py`
4. `.../03_audit_paper300_h8.py`
5. `.../04_quarantine_spatial_overlap.py`
6. `.../05_smoke_paper300_models.py`
7. `.../06_train_evaluate_paper300_h8.py`

Run each script's `--help` before the first use; they write into
`artifacts/paper300/` and expect the frozen manifest written by step 1.

## Figures

| Script | Inputs | Outputs |
| --- | --- | --- |
| `figures/make_fig4.py` | `fig4_source_panels.png`, `talor.png` (same directory) | `fig4_letter.png`, `fig4_letter.pdf` |
| `figures/make_figs3_study_area.py` | `us_counties_fips.json`, `Paper300_county_roster.csv` | `figS3_study_area.png`, `figS3_study_area.pdf` |
| `figures/plot_fig4_sealed_case.py` | Sealed work directories through the portable adapter | `Fig4_sealed_ndvi_case.png`, `.pdf` |

The scripts were authored inside the submission tree and keep its path
conventions; `figures/README.md` lists the constants to edit when running them
from this archive. Fig. 2 and Fig. S1 were drawn from
`work/ndvi_recovery_sealed_v2/reports/recovery_curves.csv`; the script that
produced those two panels is not part of this archive.

## Not included, and known limitations

- About 11 GB of training and development arrays, 3.6 GB of sealed arrays, the
  raw remote-sensing and weather archives, and training breakpoints remain on the
  project machine. This archive carries manifests, window indices, weights, code,
  logs, and derived tables, not the arrays themselves.
- `project/cropclimatex_delta_transition_experiment.py` is imported by six
  scripts in `project/` but is not part of this archive. Add it from the working
  tree before re-running steps 1-7 above; the frozen hash audit and the sealed
  replay do not depend on it.
- `figures/plot_fig4_sealed_case.py` and `figures/make_figs3_study_area.py` retain
  absolute paths from the machine that produced the figures (see
  `figures/README.md`).
- No terminal stdout/stderr transcripts were preserved beyond the `.log` files
  listed above; per-epoch histories and structured replay caches are included.

## Data availability

The archive is publicly available from the Releases page. Raw upstream data
(CropClimateX, MODIS MOD09A1.061, Daymet, and the USDA Cropland Data Layer) are
not redistributed; they remain under their original terms. The manuscript's
Data Availability statement is: "County assignments, matched windows, derived
results, model code, and frozen weights are available from the corresponding
author on reasonable request." This repository makes the archive public.

## License and citation

Code, frozen weights, the window index, and the derived tables are released under
the MIT license (`LICENSE`). Upstream data remain under their own terms and are
not redistributed here. Citation metadata is in `CITATION.cff`; please cite the
letter as well as this archive.

Corresponding author: Fengchang Xue, `001968@nuist.edu.cn`.
