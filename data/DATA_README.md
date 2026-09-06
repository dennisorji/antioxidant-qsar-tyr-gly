# Data

This project uses the DPPH antioxidant dataset associated with:

Ghironi, S., Viganò, E. L., Selvestrel, G., & Benfenati, E. (2025).  
*QSAR Models for Predicting the Antioxidant Potential of Chemical Substances*.  
*Journal of Xenobiotics, 15*(3), 80.  
DOI: `10.3390/jox15030080`

## Raw dataset

The analysis starts from:

`DPPH_30min_Dataset.xlsx`

The source dataset is publicly accessible from the authors' repository, but a separate explicit repository-level redistribution license was not identified during project documentation. The XLSX file is therefore not mirrored in this repository.

For provenance and download instructions, see:

`data/raw/README.md`

To reproduce the analysis:

1. Obtain `DPPH_30min_Dataset.xlsx` from the authors' original repository.
2. Place the file locally at:

   `data/raw/DPPH_30min_Dataset.xlsx`

3. Run:

   `notebooks/01_dpph_qsar_analysis.ipynb`

The notebook performs assay-time filtering, molecular-structure quality control, descriptor calculation, fingerprint generation, model development and validation, applicability-domain analysis, Tyr–Gly case-study evaluation, interpretation, figure generation, and results export.

## Processed data

The repository includes the project-specific standardized Tyr–Gly case-study file:

`data/processed/tyr_gly_case_study_standardized.csv`

Other processed files are generated automatically from the external source dataset and are excluded from version control through `.gitignore`, including:

- `dpph_model_ready_dataset.csv`
- `dpph_non_strict_assay_time_records.csv`
- `dpph_strict_30min_qc.csv`
- `morgan_fingerprints_2048.npz`

These files are recreated by the notebook during the analysis workflow.
