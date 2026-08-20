# Structure–Activity Modeling of Sulfonamide–Tyrosine–Glycine Peptide Hybrids for Antioxidant Discovery

A cheminformatics and machine-learning study investigating molecular determinants of DPPH radical-scavenging activity and evaluating experimentally synthesized sulfonamide–Tyr–Gly derivatives within a broader antioxidant chemical space.

---

## Project Overview

This project extends experimental undergraduate research on the synthesis, spectroscopic characterization, and antioxidant screening of two sulfonamide–Tyr–Gly derivatives with a computational structure–activity analysis.

The computational workflow combines:

* curated DPPH antioxidant activity data;
* RDKit molecular descriptors;
* Morgan molecular fingerprints;
* Random Forest regression;
* Extra Trees regression;
* random cross-validation;
* scaffold-aware cross-validation;
* DOI-grouped validation;
* applicability-domain analysis;
* molecular-descriptor interpretation;
* fingerprint/substructure interpretation; and
* case-study evaluation of the experimentally investigated Tyr–Gly derivatives.

The objective is **not** to construct a peptide-specific antioxidant model.

Instead, a broad small-molecule DPPH QSAR model is used to investigate structure–activity relationships and to determine how reliably the experimental Tyr–Gly derivatives can be evaluated within the chemical space represented by the available modeling data.

---

## Experimental Foundation

The underlying experimental project involved the synthesis and characterization of two sulfonamide–Tyr–Gly derivatives, referred to here as **Compounds 50 and 51**.

Their reported DPPH antioxidant activities were:

| Compound | Reported IC50 (mg/mL) | Reported pIC50 |
| -------- | --------------------: | -------------: |
| 50       |                  0.63 |          2.850 |
| 51       |                  0.74 |          2.793 |

Experimental measurements are kept distinct from computational predictions throughout the analysis.

---

## Research Questions

The computational study addresses four main questions:

1. How effectively can molecular descriptors and structural fingerprints model DPPH antioxidant activity across a broad chemical dataset?
2. How strongly does apparent model performance change when increasingly restrictive validation strategies are used?
3. Which molecular properties and fingerprint environments are statistically associated with DPPH activity?
4. How reliably can the trained model evaluate the experimentally investigated sulfonamide–Tyr–Gly derivatives?

---

## Dataset

The external modeling dataset was obtained from the repository accompanying:

**Ghironi, S., Viganò, E. L., Selvestrel, G., & Benfenati, E. (2025).**
*QSAR Models for Predicting the Antioxidant Potential of Chemical Substances.*
**Journal of Xenobiotics, 15(3), 80.**

DOI: `10.3390/jox15030080`

### Starting dataset

* **1,911 DPPH-active chemical substances**
* source file: `DPPH_30min_Dataset.xlsx`

Following assay-time filtering and molecular-structure quality control:

* **1,891 molecules** were retained for modeling.

---

## Data Availability

The original external source dataset is **not redistributed in this repository**.

To reproduce the complete workflow:

1. Obtain `DPPH_30min_Dataset.xlsx` from the repository accompanying the cited publication.
2. Create the following directory locally:

```text
data/raw/
```

3. Place the source dataset at:

```text
data/raw/DPPH_30min_Dataset.xlsx
```

The project-specific standardized Tyr–Gly case-study dataset is included in this repository:

```text
data/processed/tyr_gly_case_study_standardized.csv
```

Additional source-derived intermediate datasets and the generated molecular fingerprint matrix are excluded from version control and are recreated by the analysis workflow.

Additional information is provided in:

```text
data/DATA_README.md
```

---

## Molecular Representation

Two complementary molecular representations were used.

### Morgan Fingerprints

Circular Morgan fingerprints were generated using:

* **radius:** 2
* **fingerprint size:** 2,048 bits
* approximate representation: **ECFP4**

These fingerprints encode local molecular environments and provide a high-dimensional representation of chemical structure.

### RDKit Molecular Descriptors

Nine physicochemical descriptors were calculated:

* molecular weight;
* LogP;
* hydrogen-bond donor count;
* hydrogen-bond acceptor count;
* topological polar surface area;
* rotatable-bond count;
* aromatic-ring count;
* heavy-atom count; and
* fraction Csp3.

The combined representation therefore contained:

**2,048 fingerprint features + 9 molecular descriptors = 2,057 features.**

---

## Machine-Learning Models

Two ensemble tree-based regression algorithms were evaluated:

* **Random Forest Regressor**
* **Extra Trees Regressor**

Random Forest was retained as the primary model because Extra Trees showed the same general validation behavior without providing a consistent performance advantage.

---

## Validation Strategy

A major focus of this work was evaluating how model performance changes when increasingly realistic validation strategies are applied.

Three validation schemes were compared.

| Validation Strategy | Random Forest R² | Extra Trees R² |
| ------------------- | ---------------: | -------------: |
| Random 5-fold CV    |            0.712 |          0.737 |
| Scaffold-aware CV   |            0.447 |          0.455 |
| DOI-grouped CV      |            0.124 |          0.104 |

The progressive decline in performance is scientifically important.

Random cross-validation allows structurally related compounds to occur across training and validation folds and can therefore produce relatively optimistic performance estimates.

Scaffold-aware validation provides a more demanding test by separating molecules according to molecular scaffold.

DOI-grouped validation is more restrictive because compounds associated with the same publication-derived series are kept together.

The results demonstrate that **estimated QSAR performance depends strongly on how chemical generalization is evaluated**.

---

## Internal Held-Out Test

The selected Random Forest model achieved approximately:

* **MAE:** 0.277
* **RMSE:** 0.438
* **R²:** 0.792

on the internal random held-out test set.

However, performance depended strongly on structural familiarity.

| Scaffold Status             | Molecules |    R² |
| --------------------------- | --------: | ----: |
| Scaffold seen in training   |       289 | 0.824 |
| Scaffold unseen in training |        90 | 0.580 |

The strong overall random held-out performance is therefore **not interpreted as the expected performance for arbitrary novel chemical structures**.

Instead, model reliability decreases as compounds move away from the structural space represented during training.

---

## Applicability-Domain Analysis

Prediction reliability was assessed using nearest-neighbour **Morgan fingerprint Tanimoto similarity**.

For each compound, similarity to the training chemical space was evaluated.

A training-derived similarity threshold was then used to distinguish predictions:

* **inside the applicability domain**, and
* **outside the applicability domain**.

Predictions for structurally familiar compounds were substantially more reliable than predictions for structurally dissimilar molecules.

This highlights an important principle of molecular machine learning:

> A model producing a numerical prediction does not mean that the prediction is equally reliable for every chemical structure.

Prediction values must therefore be interpreted alongside information about structural similarity and chemical-domain coverage.

---

## Tyr–Gly Case Study

The two experimentally investigated sulfonamide–Tyr–Gly derivatives were evaluated using the final Random Forest model.

| Compound | Reported pIC50 | Predicted pIC50 | Maximum Tanimoto Similarity | Inside AD |
| -------- | -------------: | --------------: | --------------------------: | --------- |
| 50       |          2.850 |           3.857 |                       0.364 | No        |
| 51       |          2.793 |           3.899 |                       0.333 | No        |

Neither compound possessed a Bemis–Murcko scaffold represented in the external modeling dataset.

Both compounds are therefore treated as **out-of-domain case studies rather than conventional external-validation compounds**.

The model overpredicted their activities by approximately one pIC50 unit.

Furthermore, the small difference between the predicted activities of Compounds 50 and 51 is not considered meaningful relative to model uncertainty and their limited structural similarity to the training chemical space.

The case study therefore demonstrates why QSAR predictions for chemically novel compounds should be accompanied by explicit assessment of their applicability domain.

---

## Structure–Activity Interpretation

### Molecular Descriptors

Descriptor-level interpretation identified several physicochemical properties associated with model predictions, including:

* hydrogen-bond donor count;
* topological polar surface area;
* lipophilicity;
* fraction Csp3; and
* aromatic-ring content.

### Fingerprint Interpretation

Morgan fingerprint interpretation identified several well-supported oxygenated aromatic environments statistically associated with increased DPPH activity within the modeling dataset.

These results are interpreted as **statistical structure–activity relationships rather than direct causal mechanisms**.

Feature importance alone does not establish a chemical mechanism.

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
│   └── validation, prediction and interpretation figures
│
├── results/
│   └── model, validation, applicability-domain
│       and case-study result tables
│
├── .gitignore
├── environment.yml
└── README.md
```

---

## Start Here

The main computational workflow is contained in:

```text
notebooks/01_dpph_qsar_analysis_REVISED.ipynb
```

The notebook performs:

1. raw-data loading;
2. assay-time filtering;
3. molecular-structure quality control;
4. RDKit descriptor calculation;
5. Morgan fingerprint generation;
6. feature-matrix construction;
7. Random Forest modeling;
8. Extra Trees modeling;
9. random cross-validation;
10. scaffold-aware validation;
11. DOI-grouped validation;
12. internal held-out evaluation;
13. applicability-domain analysis;
14. descriptor-level interpretation;
15. fingerprint/substructure interpretation;
16. Tyr–Gly case-study evaluation;
17. figure generation; and
18. results export.

---

## Environment Setup

The computational environment is defined in:

```text
environment.yml
```

The environment contains:

* Python 3.11
* RDKit
* Jupyter
* NumPy
* pandas
* SciPy
* scikit-learn
* Matplotlib
* openpyxl

### Create the environment

From the repository directory, run:

```bash
conda env create -f environment.yml
```

Activate it with:

```bash
conda activate antioxidant-qsar-tyr-gly
```

Launch Jupyter:

```bash
jupyter notebook
```

Then open:

```text
notebooks/01_dpph_qsar_analysis_REVISED.ipynb
```

---

## Reproducing the Analysis

For complete reproduction:

### 1. Clone or download this repository

Navigate to the repository directory on your computer.

### 2. Obtain the external source dataset

Download:

```text
DPPH_30min_Dataset.xlsx
```

from the repository associated with the cited Ghironi et al. study.

### 3. Place the dataset at

```text
data/raw/DPPH_30min_Dataset.xlsx
```

### 4. Create and activate the Conda environment

```bash
conda env create -f environment.yml
conda activate antioxidant-qsar-tyr-gly
```

### 5. Launch Jupyter

```bash
jupyter notebook
```

### 6. Open the main notebook

```text
notebooks/01_dpph_qsar_analysis_REVISED.ipynb
```

### 7. Run the complete workflow

Use:

**Kernel → Restart Kernel and Run All Cells**

The analysis was tested successfully using a full restart-and-run workflow.

The notebook performs preprocessing, molecular representation, model fitting, validation, applicability-domain analysis, case-study evaluation, interpretation, figure generation, and results export.

Generated intermediate files excluded through `.gitignore` are recreated automatically during the workflow.

---

## Main Outputs

### Figures

The `figures/` directory contains visualizations including:

* validation-strategy comparison;
* random versus scaffold-aware validation;
* internal held-out experimental versus predicted activity;
* held-out residual analysis;
* prediction error versus Tanimoto similarity; and
* descriptor permutation importance.

Both PNG and SVG versions are provided for several figures.

### Results

The `results/` directory contains machine-readable tables including:

* model-validation comparison;
* Random Forest versus Extra Trees comparison;
* internal held-out test predictions;
* scaffold-specific held-out performance;
* applicability-domain performance;
* validation-strategy comparison;
* descriptor permutation importance;
* descriptor correlation versus importance;
* top Morgan fingerprint bits;
* interpreted Morgan fingerprint environments;
* Morgan versus combined-representation scaffold results;
* Tyr–Gly case-study predictions;
* case-study reliability analysis;
* Compound 50/51 differential fingerprint bits; and
* Compound 50/51 differential molecular environments.

---

## Key Findings

The study produced four main findings.

### 1. Random validation substantially overstates generalization

Random 5-fold validation produced substantially stronger R² values than scaffold-aware and DOI-grouped validation.

This demonstrates that apparently strong machine-learning performance can partly reflect structural or study-level similarity between training and validation compounds.

### 2. Chemical novelty reduces predictive reliability

Performance on internal held-out molecules with scaffolds already represented during training was considerably stronger than performance on unseen scaffolds.

### 3. Applicability-domain assessment is essential

Nearest-neighbour structural similarity was strongly related to prediction reliability.

Predictions should therefore be evaluated together with information about whether a compound lies within the model's represented chemical space.

### 4. The Tyr–Gly derivatives are outside the model domain

Compounds 50 and 51 had low nearest-neighbour Tanimoto similarities and possessed scaffolds absent from the external modeling dataset.

Their model predictions are therefore interpreted cautiously and are not treated as strong external validation evidence.

---

## Limitations

Several limitations should be considered when interpreting the results:

* the model was trained on a broad small-molecule DPPH dataset rather than a peptide-specific dataset;
* DPPH measurements collected across different studies may contain experimental variability;
* random splitting benefits from structural overlap between training and validation data;
* scaffold-aware validation provides a more demanding assessment of structural generalization;
* DOI-grouped validation introduces an even more restrictive study-level generalization test;
* Compounds 50 and 51 lie outside the identified applicability domain;
* molecular descriptors and fingerprint importance identify statistical associations rather than direct causal mechanisms; and
* predictions for structurally novel molecules should therefore be interpreted cautiously.

---

## Scientific Perspective

The central conclusion of this project is not simply that machine learning can be applied to antioxidant QSAR modeling.

The more important finding is that **model performance and prediction reliability depend strongly on chemical similarity, validation design, and applicability-domain coverage**.

A model that performs strongly under random validation may perform substantially less well when asked to generalize to unfamiliar scaffolds or independent publication-derived chemical series.

This project therefore emphasizes:

* chemically informed validation;
* study-aware validation;
* applicability-domain assessment;
* transparent separation of experimental and predicted values;
* interpretable structure–activity analysis; and
* cautious use of machine learning for structurally novel compounds.

---

## Tools and Libraries

* Python
* Jupyter
* RDKit
* scikit-learn
* pandas
* NumPy
* SciPy
* Matplotlib
* openpyxl

---

## Reference

Ghironi, S., Viganò, E. L., Selvestrel, G., & Benfenati, E. (2025).
*QSAR Models for Predicting the Antioxidant Potential of Chemical Substances.*
**Journal of Xenobiotics, 15(3), 80.**

DOI: `10.3390/jox15030080`

---

## Author

**Dennis Obinna Orji**
Industrial Chemistry

This repository documents the computational component of a research project combining experimental peptide chemistry, cheminformatics, and machine learning for antioxidant structure–activity analysis.
