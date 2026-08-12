# Environment Setup

Setup instructions for the Student Dropout Survival Analysis project (Bayes 26D). Do this once before running the notebooks described in `Bayes26D_Notebook_Build_Blueprint.md`.

**Prerequisite:** Python **3.10–3.13** installed. Check with `python --version`. If you don't have Python, install it from python.org (Windows) or via Homebrew/your package manager, or use Miniconda (Option B below).

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
python -c "import numpy, pandas, sklearn, imblearn, matplotlib, seaborn, pyarrow, joblib; print('Environment OK')"
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
├── Bayes 26D.csv                          # raw data (already here)
├── data_dictionary.md                     # column definitions
├── project_context_and_methodology.md     # supervisor's method
├── Bayes26D_Notebook_Build_Blueprint.md   # build instructions
├── requirements.txt                        # this environment
├── SETUP.md                                # this file
├── .venv/                                  # your environment (don't commit/share)
├── 01_data_preparation.ipynb              # Phase 1 (builds the cohorts)
├── 02_classification.ipynb                # Phase 2
├── 03_clustering_personas.ipynb           # Phase 3
├── data/processed/                        # cohort_1..4 parquet files (Phase 1 output)
├── outputs/figures/                       # saved plots
├── outputs/tables/                        # saved result tables
└── models/                                # saved trained models
```

> **Tip:** if you use git, add a `.gitignore` containing `.venv/`, `data/processed/`, `outputs/`, `models/`, and `.ipynb_checkpoints/` so you don't commit the environment or generated artifacts.

---

## Notes

- Versions in `requirements.txt` are pinned to a set verified to install and import together. If a specific pin has no wheel for your OS/Python combination, change its `==` to `>=` and reinstall.
- The optional `lifelines` package (commented out in `requirements.txt`) is only needed if you later add Phase 4 formal survival analysis (Kaplan-Meier / Cox), which is outside the current Phases 1–3 scope.
