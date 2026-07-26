# Exoplanet Disposition Classification

Classifying Kepler Objects of Interest as confirmed exoplanets, candidates, or false positives using machine learning.

**Capstone project for the BRICS Astronomy / IDIA Data Analytics program.**

---

## Overview

NASA's Kepler mission detected tens of thousands of transit-like signals, but not every signal is a genuine planet — instrumental noise, stellar variability, and eclipsing binary star systems can all mimic a real planetary transit. Historically, separating true exoplanets from these false positives has required labor-intensive manual review by astronomers.

This project builds and compares three machine learning models to classify Kepler Objects of Interest (KOI) into **CONFIRMED**, **CANDIDATE**, or **FALSE POSITIVE**, using transit and stellar characteristics — while explicitly avoiding features that would leak the target label from NASA's own vetting pipeline.

## Dataset

- **Source:** [NASA Exoplanet Archive](https://exoplanetarchive.ipac.caltech.edu/) — Kepler Objects of Interest (KOI) Cumulative Table
- **Size:** 9,564 observations, 155 raw features (reduced to ~52 after cleaning)
- **Access:** Queried programmatically via `astroquery`, no manual downloads
- **Target classes:** FALSE POSITIVE (~51%), CONFIRMED (~29%), CANDIDATE (~21%)

## Methodology

1. **Data Cleaning** — Removed fully empty columns, identifier/metadata columns, and — critically — columns that leak the target label (`koi_pdisposition`, `koi_score`, `koi_fpflag_*`), since these are outputs of NASA's own vetting pipeline rather than independent physical measurements.
2. **EDA** — Identified that signal-to-noise ratio and transit depth visibly separate false positives from genuine detections, consistent with eclipsing binary contamination.
3. **Feature Engineering** — Stratified train/test split, feature scaling for linear models, label encoding.
4. **Modeling** — Compared Logistic Regression (linear baseline), Random Forest, and Gradient Boosting.
5. **Evaluation** — Used macro-averaged precision/recall/F1 (not raw accuracy) given class imbalance, plus 5-fold cross-validation to check result stability.

## Results

| Model | Accuracy | Macro F1 |
|---|---|---|
| Logistic Regression | 0.749 | 0.673 |
| Random Forest | 0.798 | 0.759 |
| **Gradient Boosting** | **0.799** | **0.760** |

Random Forest was selected for deeper evaluation due to comparable performance, faster training, and directly interpretable feature importances.

- **Strongest predictors:** signal-to-noise ratio, planet radius, planet-to-star radius ratio
- **Hardest class:** CANDIDATE (F1 = 0.56) — consistent with it being an inherently ambiguous, not-yet-fully-resolved category in real astronomical practice
- **Cross-validation mean macro F1:** 0.707 (std = 0.058), reported as a more honest generalization estimate than the single train/test split

## Key Finding

Several measurement *uncertainty* columns ranked among the top predictive features, alongside the physical measurements themselves — suggesting the model partially learns "how well-characterized is this target" as an indirect signal, not purely the underlying transit physics. This is discussed as an explicit limitation, not hidden.

## Repository Contents

- `Capstone_Project.ipynb` — full analysis notebook (data acquisition through results interpretation)
- `README.md` — this file

## Tools

Python, pandas, NumPy, Matplotlib, Seaborn, scikit-learn, Astroquery — developed in Google Colab.

## Limitations & Future Work

- Centroid offset diagnostics and odd-even transit depth significance (both genuine eclipsing-binary indicators) were excluded for scope reasons
- No specialized technique was applied to specifically improve CANDIDATE classification
- Future extensions include hyperparameter tuning, a two-stage classification approach, and testing generalization to the newer TESS (TOI) dataset

## Citation

This research has made use of the NASA Exoplanet Archive, which is operated by the California Institute of Technology, under contract with the National Aeronautics and Space Administration under the Exoplanet Exploration Program.

## Author

**Shaeel Santosh Hasepatil**
B.Tech Computer Science, IIIT Kottayam
