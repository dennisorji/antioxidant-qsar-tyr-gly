# Structure–Activity Modeling of Sulfonamide–Tyrosine–Glycine Peptide Hybrids for Antioxidant Discovery

A cheminformatics and machine-learning study investigating molecular determinants of DPPH radical-scavenging activity and evaluating experimentally synthesized sulfonamide–Tyr–Gly derivatives within a broader antioxidant chemical space.

## Project Overview

This project extends experimental undergraduate research on the synthesis, spectroscopic characterization, and antioxidant screening of two sulfonamide–Tyr–Gly derivatives with a computational structure–activity analysis.

The computational workflow combines:

- curated DPPH antioxidant activity data;
- RDKit molecular descriptors;
- Morgan molecular fingerprints;
- Random Forest regression;
- Extra Trees regression;
- random, scaffold-aware, and DOI-grouped validation;
- applicability-domain analysis;
- molecular-descriptor interpretation;
- fingerprint/substructure interpretation;
- and case-study evaluation of the experimentally investigated compounds.

The objective is not to construct a peptide-specific antioxidant model. Instead, a broad small-molecule DPPH QSAR model is used to investigate structure–activity relationships and determine how reliably the experimental Tyr–Gly derivatives can be evaluated within the available chemical space.

## Experimental Foundation

The underlying experimental project involved the synthesis and characterization of two sulfonamide–Tyr–Gly derivatives, referred to here as Compounds 50 and 51.

Reported DPPH antioxidant activity:

| Compound | Reported IC50 (mg/mL) | Reported pIC50 |
|---|---:|---:|
| 50 | 0.63 | 2.850 |
| 51 | 0.74 | 2.793 |

These experimental measurements are kept distinct from computational predictions throughout the analysis.

## Dataset

The external modeling dataset was obtained from the repository accompanying:

Ghironi, S., Viganò, E. L., Selvestrel, G., & Benfenati, E. (2025).  
*QSAR Models for Predicting the Antioxidant Potential of Chemical Substances*.  
Journal of Xenobiotics, 15(3), 80.

DOI: 10.3390/jox15030080

Starting dataset:

- 1,911 DPPH-active chemical substances
- file: `DPPH_30min_Dataset.xlsx`

After assay-time filtering and molecular-structure quality control:

- **1,891 molecules** were retained for modeling.

## Molecular Representation

Each molecule was represented using:

### Morgan fingerprints

- radius: 2
- fingerprint size: 2048 bits
- approximately equivalent to ECFP4

### RDKit descriptors

Nine molecular descriptors were calculated:

- molecular weight
- LogP
- hydrogen-bond donors
- hydrogen-bond acceptors
- topological polar surface area
- rotatable bonds
- aromatic rings
- heavy atoms
- fraction Csp3

The final combined representation contained **2,057 features**.

## Validation Strategy

Model performance was evaluated using increasingly restrictive validation strategies.

| Validation strategy | Random Forest R² | Extra Trees R² |
|---|---:|---:|
| Random 5-fold CV | 0.712 | 0.737 |
| Scaffold-aware CV | 0.447 | 0.455 |
| DOI-grouped CV | 0.124 | 0.104 |

The decreasing performance demonstrates that conventional random splitting can provide an optimistic estimate when structurally related compounds or compounds from the same publication-derived series occur across training and validation sets.

Random Forest was retained as the primary model because Extra Trees produced the same overall validation trend without a consistent performance advantage.

## Internal Held-Out Test

The selected Random Forest model achieved approximately:

- MAE: **0.277**
- RMSE: **0.438**
- R²: **0.792**

on the internal random held-out test set.

However, this result was strongly influenced by structural familiarity.

| Scaffold status | Molecules | R² |
|---|---:|---:|
| Seen in training | 289 | 0.824 |
| Unseen in training | 90 | 0.580 |

Therefore, the random held-out result is not interpreted as expected performance for arbitrary new chemical structures.

## Applicability Domain

Prediction reliability was evaluated using nearest-neighbour Morgan fingerprint Tanimoto similarity.

Using a training-derived similarity threshold, predictions inside the applicability domain were substantially more reliable than predictions outside the domain.

This analysis demonstrates that structural similarity to the development chemical space is an important determinant of prediction confidence.

## Tyr–Gly Case Study

Compounds 50 and 51 were evaluated using the final Random Forest model.

| Compound | Reported pIC50 | Predicted pIC50 | Max Tanimoto | Inside AD |
|---|---:|---:|---:|---|
| 50 | 2.850 | 3.857 | 0.364 | No |
| 51 | 2.793 | 3.899 | 0.333 | No |

Neither compound possessed a Bemis–Murcko scaffold represented in the external modeling dataset.

Both molecules therefore represent **out-of-domain case studies** rather than external validation compounds.

The model overpredicted their activities by approximately one pIC50 unit, and the small predicted difference between Compounds 50 and 51 is not considered meaningful relative to model uncertainty.

## Structure–Activity Interpretation

Descriptor-level analysis identified several influential physicochemical properties, including:

- hydrogen-bond donor count;
- topological polar surface area;
- lipophilicity;
- fraction Csp3;
- aromatic-ring content.

Fingerprint interpretation also identified several well-supported oxygenated aromatic environments associated statistically with higher DPPH activity.

These associations are interpreted as statistical SAR relationships rather than direct causal mechanisms.

## Repository Structure

```text
Project_1_Peptide_Antioxidant_SAR/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│   └── 01_dpph_qsar_analysis_REVISED.ipynb
│
├── figures/
│
├── results/
│
└── README.md
```

## Reproducibility

The analysis notebook was tested successfully using:

**Restart Kernel → Run All**

The complete workflow executes from raw-data loading through preprocessing, feature generation, model evaluation, applicability-domain analysis, case-study evaluation, interpretation, figure generation, and results export.

## Main Conclusions

The principal finding is not simply that machine learning can predict DPPH activity.

Rather, predictive performance depends strongly on how model generalization is evaluated.

Random splitting produced strong apparent performance, while scaffold-aware and DOI-grouped validation revealed increasingly difficult generalization conditions.

The study therefore emphasizes:

- chemically informed validation;
- study-grouped validation;
- applicability-domain assessment;
- transparent separation of experimental and predicted data;
- and cautious interpretation of predictions for structurally novel compounds.

## Tools

- Python
- Jupyter
- RDKit
- scikit-learn
- pandas
- NumPy
- Matplotlib

## Author

**Orji Dennis Obinna**  
Industrial Chemistry
