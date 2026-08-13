# Environment Setup

Setup instructions for the Student Dropout Survival Analysis project (Bayes 26D). Do this once before running the four notebooks, in numbered order.

**Prerequisite:** Python **3.12–3.13** installed. Check with `python --version`. If you don't have Python, install it from python.org (Windows) or via Homebrew/your package manager, or use Miniconda (Option B below).

---

## Option A — venv + pip (recommended, lightweight)

### Windows (PowerShell)

Run these from the project folder (`...\MSc\Final Project\work`):

```powershell
# 1. Create an isolated environment in a .venv folder
python -m venv .venv

# 2. Activate it (you'll see (.venv) appear in the prompt)
.\.venv\Scripts\Activate.ps1

# 3. Upgrade pip, then install everything
python -m pip install --upgrade pip
pip install -r requirements.txt

# 4. Register this environment as a Jupyter kernel
python -m ipykernel install --user --name bayes26d --display-name "Python (bayes26d)"
```

> If PowerShell blocks activation with an execution-policy error, run once:
> `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`, then retry step 2.
> (Alternatively use Command Prompt and activate with `.\.venv\Scripts\activate.bat`.)

### macOS / Linux (bash/zsh)

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name bayes26d --display-name "Python (bayes26d)"
```

---

## Option B — conda (if you prefer Anaconda/Miniconda)

```bash
conda create -n bayes26d python=3.11 -y
conda activate bayes26d
pip install -r requirements.txt
python -m ipykernel install --user --name bayes26d --display-name "Python (bayes26d)"
```

(Installing via `pip` inside the conda env keeps versions identical to `requirements.txt`. You can use `conda install` for the big packages instead if you like, but pip is simplest here.)

---

## Verify the install

With the environment **activated**, run:

```bash
python -c "import numpy, pandas, sklearn, imblearn, statsmodels, matplotlib, seaborn, pyarrow, joblib; print('Environment OK')"
```

You should see `Environment OK` and no errors.

---

## Using the environment in your editor

- **VS Code / Antigravity:** open the project folder, then select the interpreter — `Ctrl+Shift+P` → *Python: Select Interpreter* → choose the `.venv` one. When you open a notebook, pick the **"Python (bayes26d)"** kernel (top-right of the notebook).
- **JupyterLab:** run `jupyter lab` from the activated environment and select the **"Python (bayes26d)"** kernel for each notebook.
- **Claude Code / command line:** keep the environment activated in the terminal you run the assistant from, so any code it executes uses these versions.

---

## Expected project layout

The blueprint's Phase 1 notebook creates the working folders automatically, but for reference the project should end up looking like this:

```
work/
├── Bayes 26D.csv                          # raw data — NOT in this repository, see below
├── data_dictionary.md                     # column definitions
├── requirements.txt                       # this environment
├── SETUP.md                               # this file
├── .venv/                                 # your environment (not committed)
├── 01_data_preparation.ipynb              # Phase 1 — builds the four cohorts
├── 02_classification.ipynb                # Phase 2 — classifiers and baselines
├── 03_clustering.ipynb                    # Phase 3 — personas
├── 04_survival_model.ipynb                # Phase 4 — discrete-time hazard model
├── data/processed/                        # cohort parquet files (Phase 1 output, not committed)
├── outputs/figures/                       # saved plots (committed)
├── outputs/tables/                        # saved result tables (committed)
└── models/                                # saved trained models (not committed)
```

> **The dataset is not in this repository.** `Bayes 26D.csv` contains anonymised student
> records supplied by the data provider, so neither it nor anything derived from it at student
> level is published. The notebooks will not run without it. The aggregate tables and figures
> under `outputs/` are committed in full, and every number in the dissertation can be traced
> to them.

> **Tip:** `.gitignore` excludes `.venv/`, `data/`, `models/` and `.ipynb_checkpoints/`.
> `outputs/` is deliberately committed, since it holds only aggregate results.

---

## Notes

- Versions in `requirements.txt` are pinned to a set verified to install and import together. If a specific pin has no wheel for your OS/Python combination, change its `==` to `>=` and reinstall.
- The `lifelines` package is commented out in `requirements.txt` and not used. Continuous-time
  survival methods (Kaplan-Meier, Cox proportional hazards) are out of scope because dropout in
  this dataset occurs at four discrete checkpoints. Phase 4 fits a discrete-time hazard model by
  logistic regression on person-period data, which needs `statsmodels` rather than `lifelines`.
