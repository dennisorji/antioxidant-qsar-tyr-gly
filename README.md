# Structure–Activity Modeling of Sulfonamide–Tyrosine–Glycine Peptide Hybrids for Antioxidant Discovery

A cheminformatics and machine-learning study investigating molecular determinants of DPPH radical-scavenging activity and evaluating experimentally synthesized sulfonamide–Tyr–Gly derivatives within a broader antioxidant chemical space.

---

## Project Overview

This project extends undergraduate experimental research on the synthesis, spectroscopic characterization, and antioxidant screening of two sulfonamide–Tyr–Gly derivatives with a computational structure–activity analysis.

The workflow combines:

* curated DPPH antioxidant activity data;
* RDKit molecular descriptors;
* Morgan molecular fingerprints;
* Random Forest regression;
* Extra Trees regression;
* random, scaffold-aware, and DOI-grouped validation;
* applicability-domain analysis;
* molecular-descriptor interpretation;
* fingerprint/substructure interpretation; and
* case-study evaluation of the experimentally investigated Tyr–Gly derivatives.

The objective is **not** to construct a peptide-specific antioxidant model. Instead, a broad small-molecule DPPH QSAR model is used to investigate structure–activity relationships and determine how reliably the experimental Tyr–Gly derivatives can be evaluated within the available chemical space.

---

## Experimental Foundation

The underlying experimental project involved the synthesis and characterization of two sulfonamide–Tyr–Gly derivatives, referred to here as **Compounds 50 and 51**.

Reported DPPH antioxidant activities were:

| Compound | Reported IC50 (mg/mL) | Reported pIC50 |
| -------- | --------------------: | -------------: |
| 50       |                  0.63 |          2.850 |
| 51       |                  0.74 |          2.793 |

These experimental measurements are kept distinct from computational predictions throughout the analysis.

---

## Dataset

The external modeling dataset was obtained from the repository accompanying:

**Ghironi, S., Viganò, E. L., Selvestrel, G., & Benfenati, E. (2025).**
*QSAR Models for Predicting the Antioxidant Potential of Chemical Substances.*
**Journal of Xenobiotics, 15(3), 80.**

DOI: `10.3390/jox15030080`

### Starting dataset

* **1,911** DPPH-active chemical substances
* source file: `DPPH_30min_Dataset.xlsx`

After assay-time filtering and molecular-structure quality control:

* **1,891 molecules** were retained for modeling.

### Data availability

The original external source dataset is **not redistributed in this repository**.

To reproduce the complete workflow:

1. Obtain `DPPH_30min_Dataset.xlsx` from the repository accompanying the cited publication.
2. Create the following local directory if it does not already exist:

```text
data/raw/
```

3. Place the downloaded file at:

```text
data/raw/DPPH_30min_Dataset.xlsx
```

The repository includes the project-specific standardized Tyr–Gly case-study dataset:

```text
data/processed/tyr_gly_case_study_standardized.csv
```

Additional source-derived intermediate datasets and fingerprint matrices are generated during the workflow and are excluded from version control.

See:

```text
data/DATA_README.md
```

for additional data documentation.

---

## Molecular Representation

Each molecule was represented using two complementary feature types.

### Morgan fingerprints

* radius: **2**
* fingerprint size: **2,048 bits**
* approximately equivalent to **ECFP4**

### RDKit molecular descriptors

Nine physicochemical descriptors were calculated:

1. molecular weight;
2. LogP;
3. hydrogen-bond donors;
4. hydrogen-bond acceptors;
5. topological polar surface area;
6. rotatable bonds;
7. aromatic rings;
8. heavy atoms; and
9. fraction Csp3.

The combined molecular representation contained **2,057 features**.

---

## Machine-Learning Models

Two tree-based ensemble regression algorithms were evaluated:

* **Random Forest Regressor**
* **Extra Trees Regressor**

Random Forest was retained as the primary model because Extra Trees reproduced the same overall validation pattern without providing a consistent performance advantage.

---

## Validation Strategy

A central objective of this work was to determine how strongly apparent model performance depends on the way chemical data are divided during validation.

Three validation strategies were therefore compared.

| Validation strategy | Random Forest R² | Extra Trees R² |
| ------------------- | ---------------: | -------------: |
| Random 5-fold CV    |            0.712 |          0.737 |
| Scaffold-aware CV   |            0.447 |          0.455 |
| DOI-grouped CV      |            0.124 |          0.104 |

Performance decreased substantially as validation became more restrictive.

This demonstrates that conventional random splitting can produce optimistic estimates when structurally related molecules—or compounds originating from the same publication-derived series—occur across both training and validation sets.

The comparison therefore emphasizes the importance of **chemically informed validation** when estimating QSAR generalization.

---

## Internal Held-Out Test

The selected Random Forest model achieved approximately:

* **MAE:** 0.277
* **RMSE:** 0.438
* **R²:** 0.792

on the internal random held-out test set.

However, model performance depended strongly on structural familiarity.

| Scaffold status    | Molecules |    R² |
| ------------------ | --------: | ----: |
| Seen in training   |       289 | 0.824 |
| Unseen in training |        90 | 0.580 |

The high overall random held-out R² is therefore **not interpreted as expected performance for arbitrary novel chemical structures**.

---

## Applicability-Domain Analysis

Prediction reliability was evaluated using nearest-neighbour **Morgan fingerprint Tanimoto similarity**.

A similarity threshold derived from the training chemical space was used to distinguish molecules inside and outside the model's applicability domain.

Predictions for molecules inside the applicability domain were substantially more reliable than predictions for structurally dissimilar molecules outside it.

This analysis highlights an important principle of molecular machine learning:

> A numerical prediction is not equally trustworthy for every molecule.

Structural similarity to the model-development chemical space is therefore considered alongside the predicted activity itself.

---

## Tyr–Gly Case Study

The experimentally investigated sulfonamide–Tyr–Gly derivatives were evaluated with the final Random Forest model.

| Compound | Reported pIC50 | Predicted pIC50 | Max Tanimoto | Inside AD |
| -------- | -------------: | --------------: | -----------: | --------- |
| 50       |          2.850 |           3.857 |        0.364 | No        |
| 51       |          2.793 |           3.899 |        0.333 | No        |

Neither compound possessed a Bemis–Murcko scaffold represented in the external modeling dataset.

Both compounds are therefore treated as **out-of-domain case studies**, rather than as conventional external-validation compounds.

The model overpredicted their activities by approximately one pIC50 unit. The small predicted difference between Compounds 50 and 51 is also not considered meaningful relative to the model's uncertainty and their limited structural similarity to the training space.

This case study demonstrates why QSAR predictions for chemically novel molecules should be interpreted together with applicability-domain information.

---

## Structure–Activity Interpretation

Descriptor-level analysis identified several influential physicochemical properties, including:

* hydrogen-bond donor count;
* topological polar surface area;
* lipophilicity;
* fraction Csp3; and
* aromatic-ring content.

Fingerprint interpretation also identified several well-supported oxygenated aromatic environments statistically associated with higher DPPH activity.

These relationships are interpreted as **statistical structure–activity associations rather than direct causal mechanisms**.

---

## Repository Structure

```text
antioxidant-qsar-tyr-gly/
│
├── data/
│   ├── DATA_README.md
│   └── processed/
│       └── tyr_gly_case_study_standardized.csv
│
├── notebooks/
│   └── 01_dpph_qsar_analysis_REVISED.ipynb
│
├── figures/
│   └── model-validation and interpretation figures
│
├── results/
│   └── exported model, validation, applicability-domain,
│       and case-study results
│
├── .gitignore
├── environment.yml
└── README.md
```

---

## Start Here

The complete computational workflow is contained in:

```text
notebooks/01_dpph_qsar_analysis_REVISED.ipynb
```

The notebook covers:

1. raw-data loading;
2. assay-time filtering;
3. molecular-structure quality control;
4. descriptor generation;
5. Morgan fingerprint generation;
6. model development;
7. random cross-validation;
8. scaffold-aware validation;
9. DOI-grouped validation;
10. internal held-out evaluation;
11. applicability-domain analysis;
12. descriptor interpretation;
13. fingerprint/substructure interpretation;
14. Tyr–Gly case-study analysis;
15. figure generation; and
16. results export.

---

## Environment Setup

The Python environment required for the analysis is documented in:

```text
environment.yml
```

Using Conda, create the environment with:

```bash
conda env create -f environment.yml
```

The environment can then be activated using the environment name specified in the first line of `environment.yml`.

After activation, launch Jupyter:

```bash
jupyter notebook
```

and open:

```text
notebooks/01_dpph_qsar_analysis_REVISED.ipynb
```

---

## Reproducibility

The complete analysis was tested using:

**Restart Kernel → Run All**

For full reproduction, the external source dataset must first be obtained from the cited publication repository and placed at:

```text
data/raw/DPPH_30min_Dataset.xlsx
```

The notebook then executes the workflow from raw-data loading through preprocessing, feature generation, model evaluation, applicability-domain analysis, case-study evaluation, interpretation, figure generation, and results export.

Generated intermediate files excluded by `.gitignore` are recreated automatically during the workflow.

---

## Outputs

### Figures

The `figures/` directory contains visualizations associated with:

* validation-strategy comparison;
* random versus scaffold-aware validation;
* internal held-out predictions;
* residual analysis;
* prediction error versus structural similarity; and
* descriptor permutation importance.

### Results

The `results/` directory contains machine-readable outputs including:

* model-validation comparisons;
* internal held-out predictions;
* scaffold performance;
* applicability-domain performance;
* Random Forest versus Extra Trees comparisons;
* descriptor permutation importance;
* descriptor correlation and importance analysis;
* top Morgan fingerprint bits;
* interpreted Morgan-bit environments;
* Tyr–Gly case-study predictions;
* Compound 50/51 differential fingerprint environments; and
* case-study reliability assessments.

---

## Main Conclusions

The principal finding of this project is not simply that machine learning can model DPPH antioxidant activity.

Rather, **estimated predictive performance depends strongly on how model generalization is tested**.

Random splitting produced comparatively strong apparent performance, whereas scaffold-aware and DOI-grouped validation revealed substantially more difficult generalization conditions.

The project therefore emphasizes:

* chemically informed validation;
* publication/study-grouped validation;
* applicability-domain assessment;
* transparent separation of experimental measurements and computational predictions;
* molecular-level interpretation; and
* cautious interpretation of predictions for structurally novel molecules.

The Tyr–Gly case study further demonstrates that even when a trained QSAR model produces numerical predictions for new compounds, those predictions should not be interpreted independently of the chemical domain in which the model was developed.

---

## Limitations

Several limitations should be considered when interpreting the results:

* the model is trained on a broad small-molecule DPPH dataset rather than a peptide-specific dataset;
* DPPH activity measurements may contain inter-study experimental variability;
* random validation benefits from structural overlap between training and validation data;
* scaffold-aware and DOI-grouped validation provide more demanding tests of generalization;
* Compounds 50 and 51 lie outside the model's applicability domain;
* fingerprint and descriptor importance identifies statistical associations, not direct mechanistic causation; and
* predictions for structurally novel compounds should therefore be interpreted cautiously.

---

## Tools and Libraries

* Python
* Jupyter
* RDKit
* scikit-learn
* pandas
* NumPy
* Matplotlib

---

## Reference

Ghironi, S., Viganò, E. L., Selvestrel, G., & Benfenati, E. (2025). *QSAR Models for Predicting the Antioxidant Potential of Chemical Substances*. **Journal of Xenobiotics, 15**(3), 80.

DOI: `10.3390/jox15030080`

---

## Author

**Dennis Obinna Orji**
Industrial Chemistry

This repository documents the computational component of a research project combining experimental peptide chemistry, cheminformatics, and machine learning for antioxidant structure–activity analysis.
