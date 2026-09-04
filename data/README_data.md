# Data Access

The raw data used in this study are publicly available and must be downloaded
directly from their primary sources. They are not redistributed here.

## TCGA-BRCA

| Layer | Source | URL |
|---|---|---|
| mRNA (STAR-TPM) | UCSC Xena GDC hub | https://xena.ucsc.edu |
| CNV (GISTIC2) | GDC Data Portal | https://portal.gdc.cancer.gov |
| RPPA | GDC Data Portal | https://portal.gdc.cancer.gov |
| Somatic mutations + PAM50 labels | cBioPortal | https://www.cbioportal.org/study/summary?id=brca_tcga_pan_can_atlas_2018 |

Only primary solid tumour samples (code 01) were retained, one barcode per
patient and layer, yielding 677 patients after requiring complete data across
all four layers and a valid PAM50 label.

## METABRIC

| Layer | Source | URL |
|---|---|---|
| Expression (Illumina HT-12) + CLAUDIN_SUBTYPE | cBioPortal | https://www.cbioportal.org/study/summary?id=brca_metabric |

1,756 patients used for external validation.


