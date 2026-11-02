# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

quotelogClassifier: a classifier for automatically generated event log messages (README, in Portuguese: "classificador de logs ou mensagens automaticas de eventos automaticos"). It takes a raw log line and predicts why a quote was not priced. The project is at an early stage. Only `README.md` and `data/sample.csv` are committed. `notebook/qlogClass.ipynb` exists but is empty and untracked, and no model code exists yet.

## Environment

- The Python 3.10 venv lives inside the repo at `notebook/.qlogclass`. Activate it with `source notebook/.qlogclass/bin/activate`, or call `notebook/.qlogclass/bin/python` directly.
- Installed: pandas 2.3, numpy 2.2, scikit-learn 1.7, scipy, ipykernel/ipython. No Jupyter server (jupyterlab/notebook) and no nbconvert are installed. To use the notebook, select this venv as the kernel in your editor, or pip-install a server first.
- There is no requirements file, test suite, linter or build step. Dependencies exist only in the venv.
- There is no `.gitignore`. The venv (`notebook/.qlogclass/`) and Vim swap files (`data/.sample.csv.swp`) show up as untracked, so stage files explicitly and never use `git add -A` or `git add notebook/`.

## Data: `data/sample.csv`

Format quirks:
- Delimiter is `;`
- Encoding is UTF-8 **with BOM**
- Spacing after delimiters is inconsistent (`; Docto nok` vs `;Docto nok`)

Load it with:

```python
pd.read_csv("data/sample.csv", sep=";", encoding="utf-8-sig", skipinitialspace=True)
```

Without `utf-8-sig`, the first column name becomes `﻿ds_text`. Without `skipinitialspace`, labels keep leading spaces and split into duplicate classes.

Columns:
- `ds_text`: the raw log message, which is the model input. Examples: `app_filter:Qsa nok penumper 7555666`, `spread not registered penumper 1593578`, `counterparty not registered penumper 1559357`.
- `ds_notpriced`: fine-grained label. Values: `QSA nok`, `API nok`, `Suitability nok`, `spread not registered`, `client not registered`.
- `ds_reason` and `macro_ds_notpriced`: coarse label. Values: `Docto nok`, `spread not registered`, `client not registered`. The three `app_filter:* nok` fine labels all roll up to `Docto nok`. In the current sample these two columns are identical on every labeled row.
- Rows with all three label fields empty are **unlabeled**. Exclude them from supervised training and use them for inference.

Modeling pitfalls visible in the data:
- Every message ends in `penumper <7-digit id>`. The id differs on every row and carries no class signal. Mask or strip it before vectorizing so a model can't memorize ids.
- Message wording doesn't always match label wording. For example, `counterparty not registered` maps to `client not registered`. Learn the mapping from the labels instead of assuming string equality.
