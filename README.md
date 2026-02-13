# Predictive Modelling for Drug Formulation Support — GLP-1 Receptor Excipient Screening

A machine learning pipeline for predicting the bioactivity of pharmaceutical excipients at the GLP-1 receptor, built to support early safety screening in drug formulation. The project applies cheminformatics feature engineering and regression modelling to estimate pChEMBL values for compounds sourced from the FDA excipient database.

---

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Dataset](#dataset)
- [Installation](#installation)
- [Usage](#usage)
- [Pipeline Architecture](#pipeline-architecture)
- [Model Performance](#model-performance)
- [Excipient Screening](#excipient-screening)
- [Limitations and Future Work](#limitations-and-future-work)

---

## Overview

Pharmaceutical excipients are typically considered inert, but there is growing interest in whether some may exhibit off-target biological activity. This project builds regression models trained on known GLP-1 receptor ligands to predict the pChEMBL values (a measure of binding affinity) of FDA-approved excipients, identifying candidates that may warrant further investigation.

The pipeline covers data loading and EDA, molecular feature engineering via Morgan fingerprints, training and tuning of three regression models (Random Forest, SVR, MLP), model evaluation, SHAP-based interpretability, and final prediction on a separate excipient dataset.

---

## Project Structure

```
glp1-excipient-screening/
│
├── notebooks/
│   ├── EDA_excipients_project.ipynb        # Exploratory data analysis
│   ├── SVR_model.ipynb                     # SVR training and GridSearchCV tuning
│   ├── MLP__randomsearch.ipynb             # MLP training with RandomizedSearchCV
│   └── protein_excipients_code.ipynb       # Full pipeline: all models + excipient predictions
│
├── Code_Walkthrough.md                     # Step-by-step code explanation
│
├── data/                                   # Input data (not tracked)
│   ├── glp.csv                             # GLP-1 receptor ligands with SMILES and pChEMBL values
│   ├── glp_full.csv                        # Extended version of the GLP-1 dataset
│   └── IIR_OCOMM_with_SMILES_filtered.csv  # FDA excipient dataset with SMILES
│
├── outputs/                                # Generated predictions (not tracked)
│   ├── prediction_table.csv                # Actual vs predicted pChEMBL for training compounds
│   └── predictions_table.csv              # Final excipient pChEMBL predictions
│
├── README.md
└── requirements.txt
```

---

## Dataset

**Training data** — GLP-1 receptor ligands sourced from ChEMBL and PubChem via their APIs, containing 1,331 molecules with experimentally measured pChEMBL values (range: 4.0–11.0, mean: 8.07). Each molecule is represented by its SMILES string.

**Excipient data** — FDA-approved excipients from the IIR/OCOMM database, filtered to those with valid SMILES strings. These compounds have no experimentally measured pChEMBL values and are the primary prediction targets.

Both datasets are converted to 2048-bit Morgan fingerprints (radius=2) using RDKit as the numerical feature representation for modelling.

---

## Installation

```bash
git clone https://github.com/MontzCode/glp1-excipient-screening.git
cd glp1-excipient-screening
pip install -r requirements.txt
```

**Key dependencies:**

```
pandas
numpy
scikit-learn
rdkit
shap
matplotlib
seaborn
scipy
bayesian-optimization
```

---

## Usage

Run notebooks in the following order:

```
1. EDA_excipients_project.ipynb          # Understand the data
2. SVR_model.ipynb                       # Train and tune SVR
3. MLP__randomsearch.ipynb              # Train and tune MLP
4. protein_excipients_code.ipynb        # Full pipeline with excipient predictions
```

The main pipeline notebook (`protein_excipients_code.ipynb`) runs end-to-end: it loads data, engineers features, trains all three models, evaluates them, and outputs ranked excipient predictions.

---

## Pipeline Architecture

```
GLP-1 receptor ligands (ChEMBL / PubChem)
        |
        v
EDA — distribution analysis, outlier detection, correlation matrix, PCA, KMeans clustering
        |
        v
SMILES → Morgan Fingerprints (radius=2, 2048-bit) via RDKit
        |
        v
Feature scaling (RobustScaler for MLP; StandardScaler for SVR)
        |
        v
Model training
  ├── Random Forest Regressor (5-fold cross-validation)
  ├── SVR (poly kernel → GridSearchCV tuning over C, epsilon, kernel)
  └── MLP (RandomizedSearchCV over 50 configurations, 5-fold CV)
        |
        v
Model evaluation (R², RMSE, MAE, explained variance)
        |
        v
SHAP interpretability + permutation feature importance
        |
        v
FDA excipient SMILES → Morgan Fingerprints → pChEMBL predictions
        |
        v
Ranked excipient output
```

---

## Model Performance

Three models were trained and evaluated. The MLP after randomised hyperparameter search achieved the best performance.

| Model | R² | RMSE | Notes |
|---|---|---|---|
| Random Forest | 0.77 | 0.78 | Best overall; used for final excipient screening |
| MLP (tuned) | 0.77 | 0.79 | Matched RF after RandomizedSearchCV (50 configs, 5-fold CV) |
| SVR (initial) | 0.50 | 1.22 | Baseline with poly kernel before full tuning |

Cross-validated MSE for the tuned MLP: **1.047** (5-fold KFold).

Hyperparameter search for the MLP covered hidden layer sizes (50–500 neurons), activation functions (relu, tanh, logistic), solvers (adam, lbfgs, sgd), learning rate schedules, regularisation strength (alpha), and early stopping.

---

## Excipient Screening

The validated Random Forest model was applied to the FDA excipient dataset. Each excipient's SMILES string was converted to a Morgan fingerprint and passed through the trained pipeline to generate a predicted pChEMBL value.

Results were ranked by predicted bioactivity to surface excipients with the highest estimated affinity for the GLP-1 receptor. Lysine Monohydrate was identified as a notable candidate based on its predicted pChEMBL score, suggesting it warrants further experimental investigation as a potentially bioactive excipient in GLP-1 receptor drug formulations.

SHAP analysis and permutation feature importance were used to identify which molecular fingerprint bits drove predictions, providing interpretability for the screening results.

---

## Limitations and Future Work

The model is trained exclusively on GLP-1 receptor ligands, so predictions are specific to this target and should not be generalised to other receptors without retraining. pChEMBL values span a wide range (4–11) with a mean around 8, and the dataset reflects the known chemical space of GLP-1 ligands which may not fully represent the structural diversity of excipients.

Potential improvements include expanding the feature representation beyond Morgan fingerprints to include physicochemical descriptors (logP, molecular weight, TPSA), experimenting with graph neural networks that operate directly on molecular structure, and validating top predictions experimentally.

---

## References

Data sourced from ChEMBL and PubChem APIs. Excipient SMILES from the IIR/OCOMM FDA excipient database.
