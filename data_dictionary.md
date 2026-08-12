# Data Dictionary — Student Dropout Prediction Dataset

## Project Context

**Title:** Predicting Student Dropout Before It Happens: AI for EdTech Retention

**Problem Statement:**
Can predictive models be developed to accurately identify students at risk of disengagement or dropout in online learning environments?

**Data Source:**
Real data collected from online summer programming boot camps (Python for beginners to advanced) run in partnership with multiple UK universities including Imperial College London, City University London, UCL, Birkbeck, and the University of Manchester. Data is aggregated and harmonised across approximately 5 years of cohorts.

**Modelling Goals:**
1. **Classification (Supervised Learning):** Predict whether a student will drop out (binary: Y/N). Suggested models: Logistic Regression, Decision Tree, Random Forest, Neural Networks.
2. **Clustering (Unsupervised Learning):** Identify distinct student learning personas based on engagement and performance patterns (e.g., low-engagement/high-performance, high-engagement/low-performance). These profiles can inform targeted instructor interventions.

---

## Course Structure

The course is structured as a sequence of 6 live Zoom lecture sessions, 3 tests, individual and group coursework, culminating in a final grade. The dataset columns follow this exact chronological order:

```
[Session 1] [Session 2]  ──►  [Test 1]
[Session 3] [Session 4]  ──►  [Test 2]
[Session 5]              ──►  [Test 3]
[Session 6]
[Individual Coursework]
[Group Coursework]
──► [Final Grade]

Engagement (throughout): Forum Questions, Forum Answers, Office Hour Visits
```

> **Important:** The data has been harmonised across universities with slightly different timelines. Do NOT assume fixed week/date mappings to sessions. Sessions are numbered sequentially, not time-indexed.

---

## Column Definitions

### Demographic / Background Features

| Column Name | Data Type | Values | Description |
|---|---|---|---|
| `External` | Categorical (binary) | `Y` / `N` | Whether the student is external to the hosting university. `Y` = the student is enrolled at a *different* university and attending this course from outside (e.g., a Leeds University student taking a Manchester University course). `N` = the student is enrolled at the hosting university itself. All participants must be currently enrolled at *some* university as a minimum barrier to entry. |
| `Year` | Categorical (ordinal) | `first` / `second` / `third` | The student's year of study at their *home* university, not the year of this online course. Indicates academic experience level. |

---

### Session Attendance Features
*Each session column records the percentage of that live Zoom lecture the student viewed, as a value from 0 to 100 (integer). Low values suggest disengagement; missing values indicate the student had already dropped out before this session occurred.*

| Column Name | Data Type | Range | Description |
|---|---|---|---|
| `session 1` | Numeric (integer) | 0–100 | % of Zoom lecture session 1 viewed. Occurs before Test 1. |
| `session 2` | Numeric (integer) | 0–100 | % of Zoom lecture session 2 viewed. Occurs before Test 1. |
| `session 3` | Numeric (integer) | 0–100 | % of Zoom lecture session 3 viewed. Occurs before Test 2. |
| `session 4` | Numeric (integer) | 0–100 | % of Zoom lecture session 4 viewed. Occurs before Test 2. |
| `session 5` | Numeric (integer) | 0–100 | % of Zoom lecture session 5 viewed. Occurs before Test 3. |
| `session 6` | Numeric (integer) | 0–100 | % of the final Zoom lecture session viewed. |

---

### Assessment / Grade Features
*Letter grades awarded for formal assessments. Grading scale: A (best) → B → C → D → F (fail). Missing values indicate the student had dropped out before this assessment was reached.*

| Column Name | Data Type | Values | Description |
|---|---|---|---|
| `test 1` | Categorical (ordinal) | `A` / `B` / `C` / `D` / `F` | Grade on the first online test, administered after sessions 1 and 2. |
| `test 2` | Categorical (ordinal) | `A` / `B` / `C` / `D` / `F` | Grade on the second online test, administered after sessions 3 and 4. |
| `test 3` | Categorical (ordinal) | `A` / `B` / `C` / `D` / `F` | Grade on the third online test, administered after session 5. |
| `ind cw` | Categorical (ordinal) | `A` / `B` / `C` / `D` / `F` | Grade for the **individual coursework** component. |
| `group cw` | Categorical (ordinal) | `A` / `B` / `C` / `D` / `F` | Grade for the **group coursework** component. |
| `final grade` | Categorical (ordinal) | `A` / `B` / `C` / `D` / `F` | The student's overall final course grade. |

---

### Engagement / Interaction Features
*Count-based features tracking how actively students participated in the learning community. These are total counts accumulated over the full course duration.*

| Column Name | Data Type | Range | Description |
|---|---|---|---|
| `forum Q` | Numeric (integer) | ≥ 0 | Total number of **questions** the student posted on the course discussion forum (to other students or to the lecturer). |
| `forum A` | Numeric (integer) | ≥ 0 | Total number of **answers** the student posted on the course discussion forum. |
| `office hour visits` | Numeric (integer) | ≥ 0 | Total number of virtual office hour sessions the student attended. |

---

### Target Variable

| Column Name | Data Type | Values | Description |
|---|---|---|---|
| `dropout` | Categorical (binary) | `Y` / `N` | **The prediction target.** `Y` = the student dropped out at some point during the course. `N` = the student completed the course. See dropout archetypes below. |

---

## Critical Notes for Modelling

### ⚠️ Missing Values

**Missing values in this dataset are NOT errors or omissions. They are semantically meaningful.**

A missing value in any session, test, or coursework column means the student had already dropped out *before* that point in the course. The columns progress chronologically, so a student with data up to `test 1` but missing everything from `session 3` onwards has implicitly dropped out after Test 1.

**Consequence:** Standard imputation strategies (e.g., replacing with column mean, median, or mode) are methodologically incorrect here. Imputing a value for a column where the student had already left misrepresents reality and will corrupt all downstream modelling.


---

### Dropout Archetypes

The `dropout = Y` label covers two very different student types. Being aware of these during EDA and modelling is important:

**1. "Tourist" Dropout (Early, Low-Cost)**
- Drops out very early in the course, typically after or even before Test 1
- Characteristic pattern: External student, near-zero session attendance (e.g., 0% session 1, 4% session 2), F on Test 1, then all subsequent columns are missing
- These students never really engaged — they registered but were clearly not suitable or committed

**2. "Administrative Dropout" (Late, High-Cost)**
- Completed the entire course — all sessions attended, all tests taken, all coursework submitted
- Has a populated `final grade` and all other columns filled in
- BUT is still labelled `dropout = Y` because they failed to pay the final installment of their tuition fee and therefore cannot receive their certificate
- Most expensive dropout type: consumed all resources but generated no revenue/credential
- These will look like completers in the feature data, making them hard to predict from behaviour alone

---

### Data Quality & Harmonisation

- The dataset is described as **very clean** — no accidental missing values, no inconsistencies
- It has been **harmonised across 5 years and multiple universities** with slightly different course timelines. This means session numbers are used as the common unit rather than calendar weeks
- Do NOT attempt to map sessions to specific calendar weeks or payment installment periods — this information is not reliable across the harmonised dataset
- Some demographic fields (e.g., nationality) existed in the raw data but were removed for **privacy / GDPR compliance** before this dataset was shared

---

### Class Imbalance

Online learning platforms typically have very high dropout rates (90–95% on open MOOCs). The dropout rate in this dataset may be significantly imbalanced (more dropouts than completers, or vice versa depending on this specific programme's selectivity). Check the class distribution early in EDA and account for this in model evaluation — accuracy alone is a misleading metric. Use metrics such as F1-score, precision/recall, ROC-AUC.

---

### Feature Engineering Hints

Given the sequential/temporal structure of the data, consider engineering the following:

- **Cumulative session engagement:** Rolling average of session attendance up to each test
- **Forum ratio:** `forum A / (forum Q + forum A)` — answerer vs. asker behaviour
- **Grade trajectory:** Did grades improve or decline across Test 1 → 2 → 3?
- **Ordinal encoding of grades:** A=4, B=3, C=2, D=1, F=0 for numerical modelling

---

## Summary Table

| Column | Type | Role | Values / Range |
|---|---|---|---|
| `External` | Categorical | Feature | `Y` / `N` |
| `Year` | Categorical (ordinal) | Feature | `first` / `second` / `third` |
| `session 1` | Numeric | Feature | 0–100 (% viewed) |
| `session 2` | Numeric | Feature | 0–100 (% viewed) |
| `test 1` | Categorical (ordinal) | Feature | `A` `B` `C` `D` `F` |
| `session 3` | Numeric | Feature | 0–100 (% viewed) |
| `session 4` | Numeric | Feature | 0–100 (% viewed) |
| `test 2` | Categorical (ordinal) | Feature | `A` `B` `C` `D` `F` |
| `session 5` | Numeric | Feature | 0–100 (% viewed) |
| `test 3` | Categorical (ordinal) | Feature | `A` `B` `C` `D` `F` |
| `session 6` | Numeric | Feature | 0–100 (% viewed) |
| `ind cw` | Categorical (ordinal) | Feature | `A` `B` `C` `D` `F` |
| `group cw` | Categorical (ordinal) | Feature | `A` `B` `C` `D` `F` |
| `final grade` | Categorical (ordinal) | Feature | `A` `B` `C` `D` `F` |
| `forum Q` | Numeric | Feature | Integer ≥ 0 |
| `forum A` | Numeric | Feature | Integer ≥ 0 |
| `office hour visits` | Numeric | Feature | Integer ≥ 0 |
| `dropout` | Categorical (binary) | **TARGET** | `Y` (dropped out) / `N` (completed) |
