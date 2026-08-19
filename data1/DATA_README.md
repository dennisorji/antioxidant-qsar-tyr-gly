# Data

This project uses a public DPPH antioxidant dataset associated with:

Ghironi, S., Viganò, E. L., Selvestrel, G., & Benfenati, E. (2025).
*QSAR Models for Predicting the Antioxidant Potential of Chemical Substances*.
Journal of Xenobiotics, 15(3), 80.
DOI: 10.3390/jox15030080

## Raw dataset

The analysis starts from:

`DPPH_30min_Dataset.xlsx`

The file is not redistributed in this repository because a separate explicit
redistribution license for the dataset was not identified during project
documentation.

To reproduce the analysis:

1. Obtain `DPPH_30min_Dataset.xlsx` from the repository accompanying the
   published study.
2. Place the file at:

   `data/raw/DPPH_30min_Dataset.xlsx`

3. Run:

   `notebooks/01_dpph_qsar_analysis_REVISED.ipynb`

The notebook performs assay-time filtering, molecular-structure quality
control, descriptor calculation, fingerprint generation, model evaluation,
applicability-domain analysis, and results export.

## Processed data

Only the project-specific Tyr–Gly case-study file is intended for public
distribution:

`data/processed/tyr_gly_case_study_standardized.csv`

Other processed files are generated automatically from the external source
dataset and are therefore excluded from version control through `.gitignore`.
