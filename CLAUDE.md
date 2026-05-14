# CLAUDE.md

This file provides guidance to Claude Code when working in this repository.

## Project Overview

Wave Energy Converter (WEC) farm optimization research project.
Each row in the data is a candidate farm layout — a set of 2D
positions for the WEC devices — paired with the resulting power
output of each device and the farm as a whole.

The dataset comes from a study published at GECCO (Best Paper award)
and was generated using the University of Adelaide's Phoenix HPC
service. The original purpose was to train a fast surrogate model
that approximates expensive hydrodynamic simulations.

## Goals

- Primary: build a model that predicts `Total_Power` from device
  positions (`X1, Y1, ..., Xn, Yn`) — i.e., a surrogate model
  replacing expensive hydrodynamic simulation
- Secondary: predict per-device power (`Power1, ..., Powern`) given
  the full layout — useful for understanding which positions in
  the array tend to underperform
- Exploratory: characterize the relationship between layout
  geometry and farm-level interference (q-factor)

## Data

All data lives in `data/WEC/` as CSVs. Each row is one farm
layout (a "scenario").

| File | Location | Devices | Rows |
|------|----------|---------|------|
| `WEC_Perth_49.csv` | Perth, Australia | 49 | 54,000 |
| `WEC_Perth_100.csv` | Perth, Australia | 100 | 9,600 |
| `WEC_Sydney_49.csv` | Sydney, Australia | 49 | 54,000 |
| `WEC_Sydney_100.csv` | Sydney, Australia | 100 | 9,600 |

For a description of columns, units, the q-factor metric, and known
quirks of the data, see `data/DATA_DICTIONARY.md`. **Read it before
suggesting any analysis** — column semantics aren't obvious from
names alone.

Note: `data/WEC_Perth_49.csv` at the root of `data/` is a duplicate
of `data/WEC/WEC_Perth_49.csv`. Use the one in `data/WEC/` as
canonical and ignore the root-level copy.

## Key data caveats

- "No missing values" per the source, but expect physical
  constraints: positions can't overlap, are bounded by farm area,
  etc. Verify these hold before assuming clean data.
- The two array sizes (49 vs. 100) have very different row counts
  (54k vs. 9.6k) — likely because 100-device simulations are more
  expensive. Plan accordingly: train/test sizes are not symmetric.
- Perth and Sydney have different wave climates (Perth is more
  energetic), so model performance and optimal layouts will
  differ between locations. Don't pool the two without thought.
- `Total_Power` is approximately the sum of `Power1...Powern`, but
  not exactly — the q-factor captures interaction effects that
  make the whole differ from the sum. Worth verifying the
  relationship empirically.

## Environment

- UCSD Datahub (8 CPU, 16GB RAM)
- Python via JupyterLab in browser
- Stack: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`
- The 49-device files are ~54k rows × 99 columns — fits in RAM
  comfortably but is large enough that some operations
  (e.g., pairplots over all features) are infeasible

## Project structure

data/
WEC/                    # Canonical CSVs (use these)
DATA_DICTIONARY.md      # Column descriptions and notes
notebooks/                # Jupyter notebooks
figures/                  # Saved plots (create as needed) 

## Conventions

- Path setup: `from pathlib import Path; PROJECT = Path.home() / "projects" / "wec-analysis"`
- Notebooks named `NN-description.ipynb`
- For initial EDA, **start with `WEC_Perth_49.csv`** — smaller
  feature space, more rows, easier to reason about
- When modeling, treat the four files as four separate problems
  unless explicitly combining them — different locations and array
  sizes are not directly comparable
- For position-based features, exploit the structure: it's often
  more useful to engineer summary statistics (centroid, spread,
  nearest-neighbor distances) than to feed raw `X1, Y1, ..., Xn, Yn`
  directly to a model

## Common commands

```bash
# Quick inspection
python -c "import pandas as pd; df = pd.read_csv('data/WEC/WEC_Perth_49.csv'); print(df.shape); print(df.columns.tolist()[:10], '...')"

# Verify Total_Power vs. sum of individual powers
python -c "import pandas as pd; df = pd.read_csv('data/WEC/WEC_Perth_49.csv'); cols = [f'Power{i}' for i in range(1, 50)]; print((df['Total_Power'] - df[cols].sum(axis=1)).describe())"
```

## Working notes

(Update as the project progresses)

- [setup] Project initialized; 4 CSVs from GECCO paper dataset
- [TODO] Confirm row count per file and Total_Power = sum(Power_i) relationship
- [TODO] Decide modeling target: full Total_Power, q-factor, or both
- [TODO] Choose feature representation strategy (raw coords vs. engineered geometry features)