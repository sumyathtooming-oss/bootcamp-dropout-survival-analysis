# Predicting Student Dropout Before It Happens

A cohort-based survival analysis of retention in an online programming bootcamp.

MSc Business Analytics research project (BBM110), Bayes Business School, City St George's,
University of London. Supervisor: Dr Ahmad Abu-Khazneh.

## The question

Most dropout prediction treats leaving as a single binary outcome, which tells an institution
*who* is at risk but not *when*. This project asks whether reframing dropout as a sequence of
checkpoint-specific risks gives a more actionable picture than one binary model.

18,724 students are observed at four checkpoints — Tests 1 to 3 and certification — with each
cohort containing only those who survived the previous checkpoint.

## What the analysis found

- The hazard is not monotonic: 6.5%, 13.2%, 10.4%, then 18.0% at certification.
- Risk factors are checkpoint-specific. A likelihood-ratio test rejects constant effects,
  χ²(12) = 2,783.4, p < 0.001. The dominant predictor moves from attendance at Test 1
  (OR 0.49) to attainment at Tests 2 and 3 (OR 0.42, 0.45), to neither at certification.
- The certificate event is administrative rather than academic: performance stops
  discriminating there (p = 0.128).
- **A leakage finding.** Engagement totals amortised across a student's active cohorts encode
  the outcome — when a student is in their final cohort the amortised value equals the
  untruncated total and is a whole number. A rule reading only whether those values contain
  decimals achieves perfect recall. A held-out test set cannot detect this, because the
  encoding is present on both sides of the split. The same failure recurred independently when
  a binary baseline was median-imputed, lifting F1 from 0.677 to 0.868.

Headline results are reported without the amortised features. The inflated versions are kept in
the dissertation appendix for transparency.

## Notebooks

Run in order. Each writes intermediate data that the next one reads.

| Notebook | Produces |
|---|---|
| `01_data_preparation.ipynb` | Cohort construction, feature engineering, engagement amortisation |
| `02_classification.ipynb` | Model screening, tuning, the leakage diagnosis, binary baselines |
| `03_clustering.ipynb` | Quadrant and k-means personas, composition across cohorts |
| `04_survival_model.ipynb` | Discrete-time hazard model on 65,075 person-periods |

Cell numbers cited in the dissertation are execution counts from a clean sequential run.

<!-- If Notebooks 1 to 3 were developed jointly, say so here, and state that Notebook 4,
     the dissertation, and all interpretation are your own. Delete this comment either way. -->

## Data

**The dataset is not in this repository.**

The records are anonymised student data supplied by Citibank and Lon.ai. Neither the source file
nor any file derived from it at student level is published, so `data/` and the models directory
are excluded by `.gitignore`.

The aggregate result tables under `outputs/tables/` contain no individual records and are
included in full, as are the figures under `outputs/figures/`. Every number reported in the
dissertation can be traced to those files.

The dataset may be available on request, subject to the data provider's agreement.

## Running it

```bash
pip install -r requirements.txt
jupyter lab
```

Results were produced on Python 3.13.5 (Notebooks 1, 2 and 4) and Python 3.12.12 (Notebook 3).
Use Python 3.12 or 3.13. Random seed is fixed at 42 throughout; the test split is 20 per cent
stratified and cross-validation is 5-fold stratified on the training split only.

Notebook 2 is the slow one — it runs an exhaustive grid search across five algorithms and four
cohorts. Fitted models are cached under `data/processed/_cache_v1/`, so a second run is fast.
That cache is not in the repository and will be rebuilt on first run.

Launch Jupyter from the repository root. The notebooks use relative paths, so running from
elsewhere writes outputs to the wrong place.

## Output

```
outputs/
├── tables/    21 CSVs — every table in the dissertation, plus full grid results
└── figures/   PNGs — every figure in the dissertation and appendix
```

## Author

Su Myat Htoo — MSc Business Analytics, 2026.
