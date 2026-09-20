# When is multi-omics integration justified?

Analysis outputs and configuration for the manuscript:

> **When is multi-omics integration justified? Separating predictive complementarity
> from prediction-level information structure in breast cancer subtyping**
> Fatima Zahra Belharar, Ouafae Belharar, Sara Retal, Nassim Kharmoum, Soumia Ziti

---

## Overview

This repository contains the numerical outputs supporting every table of the
manuscript and of Supplementary Tables S1–S6, together with the fixed
configuration and the cross-validation partitions needed to reproduce them.

Raw molecular data are not redistributed here (see **Data availability** below).

---

## Analysis at a glance

| | |
|---|---|
| Discovery cohort | TCGA-BRCA, 677 patients, 4 omics layers |
| Target | PAM50 subtype (5 classes) |
| Panels | all 15 non-empty subsets of the four layers |
| Classifiers | random forest, XGBoost, RBF-kernel SVM |
| Representation | 50 components per layer, fitted within training folds only |
| Resampling | 5 folds x 10 repetitions, seed 42, shared by every panel and classifier |
| Unit of inference | the cross-validation repetition (n = 10), not the fold |
| Hyperparameters | fixed a priori; no hyperparameter search, no nested cross-validation |
| Inference | exact sign-flip permutation (2^10 = 1,024 assignments); exact Spearman permutation (6! = 720 orderings) |
| Multiplicity | Benjamini-Hochberg, family stated per analysis |
| External cohort | METABRIC, 1,756 patients, two transfer protocols |

The 50 fixed partitions are provided in `config/cv_splits.json` and were reused
unchanged across all panels, classifiers and downstream analyses.

---

## Where each published number comes from

`results/panel_fold_results.csv` is the primary analysis output: one row per
panel, classifier and split, with balanced accuracy, macro-F1 and accuracy.
The summaries for Table 2, Table 3, Table 4, S1c, S2 and S3 are derived from the
recorded cross-validation results under the fixed configuration; no models are
refitted for these analyses. The partial information decomposition is the
exception: it operates on out-of-fold predictions obtained by refitting the four
single-layer models, which were accepted only after reproducing the recorded
per-fold metrics (see `validation/pid_metric_reconstruction_audit.csv`).

### Manuscript

| Table | File |
|---|---|
| Table 1a — layer representations | `metadata/representation_summary.csv` |
| Table 1b — classifier configurations | `config/analysis_config.json`, `config/pid_config.json` |
| Table 2 — balanced accuracy, 15 panels x 3 classifiers | `results/panel_fold_results.csv` |
| Table 3 — leave-one-omics-out ΔBA | `results/contribution_leave_one_omics_out.csv` |
| Table 3 — exact Shapley values | `results/performance_shapley_summary.csv` |
| Figure 2 — Shapley by classifier | `results/performance_shapley_summary.csv` |
| Table 4 — predictive redundancy R_pred | `results/rpred_repeat_summary.csv` |
| Table 5 — prediction-level PID atoms | `results/pid_repeat_summary.csv` |
| Table 6a — TCGA/METABRIC importance concordance | `results/tcga_metabric_gene_importance_correlation.csv` |
| Table 6b — external transfer, two arms | `results/metabric_direct_transfer_performance_two_arms.csv` |
| Table 6b — per-class recall, two arms | `results/metabric_direct_transfer_per_class_two_arms.csv` |

### Supplementary tables

| Table | File |
|---|---|
| S1a — input provenance and SHA-256 digests | `metadata/preprocessing_report.json` |
| S1b — software environment and configuration | `config/analysis_config.json`, `requirements.txt` |
| S1c — secondary metrics (macro-F1, accuracy) | `results/panel_fold_results.csv` |
| S1d — PID estimator validation | `validation/pid_self_tests.csv`, `validation/pid_bootstrap_convergence.json` |
| S2 — integration gains | `results/integration_gain_summary.csv` (repetition-level data: `results/integration_gain_repeat_level.csv`) |
| S3 — non-inferiority assessment | `results/minimal_panel_noninferiority.csv` |
| S4 — agreement between BROJA and Williams-Beer I_min | `results/imin_broja_rank_correlations.csv` |
| S5 — concordance R_pred x PID, both estimators | `results/rpred_pid_rank_correlations.csv` |
| S6 — top genes by attribution | `results/top30_genes_by_classifier.csv` |

### Supporting files

| File | Role |
|---|---|
| `results/oof_single_omics_predictions.csv` | out-of-fold predictions of the four single-layer models (81,240 rows); the sources entering the partial information decomposition |
| `results/pid_patient_cluster_bootstrap_summary.csv` | patient-clustered bootstrap intervals for the PID atoms |
| `results/gene_importance_summary.csv` | gene-level attribution scores, all genes |
| `validation/pid_metric_reconstruction_audit.csv` | verifies that the models refitted to obtain out-of-fold predictions reproduce the recorded per-fold metrics |
| `validation/shap_metric_reconstruction_audit.csv` | same check for the attribution stage |
| `metadata/cohort_manifest.csv` | per-patient inclusion record, discovery cohort |
| `metadata/metabric_cohort_manifest.csv` | per-patient record, external cohort |
| `results/pam50_overlap_by_top_n.csv` | PAM50 overlap across top-ranked thresholds, supporting the plausibility assessment described in Methods §2.7 |
| `config/analysis_config.json` | consolidated analysis configuration; every field is taken verbatim from the run metadata |
| `notebooks/DataDownload_preparation.ipynb` | acquisition and preparation of the public input data |
| `notebooks/Preprocessing.ipynb` | within-fold preprocessing, shared partitions, and panel evaluation |
| `notebooks/Pipeline.ipynb` | the seven downstream evidence levels, from performance to external validation |

The notebooks are run in this order: `DataDownload_preparation.ipynb`, then
`Preprocessing.ipynb`, then `Pipeline.ipynb`. Set `TCGA_BRCA_DATA_DIR` to the
directory holding the public input data before running them; no local paths are
hard-coded.

---

## Statistical conventions

Integration gains are tested against each panel's best constituent single-layer
panel with an **exact two-sided sign-flip test**, followed by Benjamini-Hochberg
correction across the eleven multi-layer panels within each classifier.
`results/integration_gain_summary.csv` is the canonical summary; the
per-repetition differences it is computed from are in
`results/integration_gain_repeat_level.csv`.

Confidence intervals are nonparametric bootstrap intervals over the ten
repetition-level values (10,000 replicates). The partial information
decomposition uses a separate patient-clustered bootstrap (1,000 replicates), in
which each resampled patient carries its predictions from all ten repetitions.

---

## Data availability

Raw data are public and are not redistributed here.

**TCGA-BRCA** — mRNA from the UCSC Xena GDC STAR-TPM matrix
(https://xena.ucsc.edu), gene-level copy number from GISTIC2 and RPPA from the
Genomic Data Commons (https://portal.gdc.cancer.gov), somatic mutations and
PAM50 annotations from cBioPortal, study `brca_tcga_pan_can_atlas_2018`
(https://www.cbioportal.org).

**METABRIC** — expression and the `CLAUDIN_SUBTYPE` annotation from cBioPortal,
study `brca_metabric`.

SHA-256 digests of every input file are recorded in
`metadata/preprocessing_report.json`.

---

## Scope

This repository contains only the artefacts supporting the analyses reported in
the manuscript. Exploratory analyses that are not part of the reported study are
not included. Per-split SHAP value matrices and per-fold feature-selection records are not
versioned here because the aggregated summaries they produce are provided in
`results/`. Local file paths have been removed from the configuration and
provenance files; SHA-256 digests and file sizes are retained.

---

## License

Code: MIT (see `LICENSE`). Results, documentation and derived data files:
CC BY 4.0.

---

## Contact

Fatima Zahra Belharar — fatimazahra_belharar@um5.ac.ma
IPSS Team, Faculty of Sciences, Mohammed V University, Rabat, Morocco
