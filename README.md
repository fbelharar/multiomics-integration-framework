# Multi-Omics Integration Framework

Code and results for the paper:

> **When Does Multi-Omics Integration Provide Complementary Predictive Information?
> An Evidence-Based Decision Framework with External Validation in Breast Cancer**
> Fatima Zahra Belharar, Ouafae Belharar, Sara Retal, Nassim Kharmoum, Soumia Ziti
> *Journal of Biomedical Informatics* (under review)

---

## Overview

This repository contains the analysis results, configuration, and data-access
instructions reproducing every table and figure of the manuscript.

The framework evaluates whether multi-omics integration is warranted through
seven successive levels of evidence:

1. Predictive performance across all 15 omics panels × 3 classifiers
2. Layer attribution — leave-one-omics-out and exact Shapley decomposition
3. Panel sufficiency — prespecified non-inferiority criterion (δ = 0.03)
4. Pairwise predictive redundancy (R_pred index)
5. Model-free information structure — partial information decomposition (PID)
6. Biological plausibility — gene-level attribution vs. PAM50 signature
7. External reproducibility — METABRIC (n = 1,756), two transfer protocols

---

## Data

Raw data are not redistributed here. They are publicly available from:

- **TCGA-BRCA** — mRNA (UCSC Xena GDC STAR-TPM), CNV (GISTIC2), RPPA
  (replicate-base-normalised), somatic mutations + PAM50 labels (cBioPortal
  `brca_tcga_pan_can_atlas_2018`):
  - https://portal.gdc.cancer.gov
  - https://xena.ucsc.edu
  - https://www.cbioportal.org/study/summary?id=brca_tcga_pan_can_atlas_2018

- **METABRIC** — expression (Illumina HT-12) + CLAUDIN_SUBTYPE annotation:
  - https://www.cbioportal.org/study/summary?id=brca_metabric

SHA-256 digests of all input files used in the analysis are listed in
`results/input_digests.csv` (Supplementary Table S1a of the manuscript).

---

## Results

All result files are in `results/`. Each file corresponds to a section of
the manuscript:

| File | Manuscript section |
|---|---|
| `contribution_shapley_summary.csv` | §3.1, Table 3 (Shapley values) |
| `contribution_leave_one_omics_out.csv` | §3.1, Table 3 (ΔBA) |
| `q1_multiomics_gains.csv` | §3.2, Supplementary Table S2 |
| `metabric_direct_transfer_performance_two_arms.csv` | §3.8, Table 7b |
| `rpred_matrix.csv` | §3.4, Table 4 |
| `pid_results.csv` | §3.5, Table 5 |

---

## Configuration

All hyperparameters are fixed a priori in `config/analysis_config.json`.
No nested cross-validation or hyperparameter search was performed.
The shared repeated cross-validation design (5 folds × 10 repetitions,
seed 42) is instantiated once and reused by every panel, classifier, and
downstream analysis.

---

## Reproducibility

- Python 3.x — package versions in `config/requirements.txt`
- All preprocessing fitted within training folds only
- Unit of inference: the repetition (not the fold)
- Exact tests throughout: sign-flip permutation (2¹⁰ = 1,024 assignments),
  exact Spearman permutation (6! = 720 orderings)
- Benjamini–Hochberg correction with family stated per analysis

---

## License

MIT License — see `LICENSE`.

---

## Contact

Fatima Zahra Belharar — fatimazahra_belharar@um5.ac.ma
IPSS Team, Faculty of Sciences, Mohammed V University, Rabat, Morocco
