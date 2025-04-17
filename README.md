# Predictive-Modelling-Project

Predicting pChEMBL Values of FDA-Approved Inactive Ingredients

This project investigates the potential bioactivity of excipients—traditionally considered inactive ingredients—using machine learning models to predict their pChEMBL values. By focusing on excipient interactions with the GLP-1 receptor, particularly in the context of Semaglutide formulations, we aim to improve drug stability, bioavailability, and manufacturing cost-efficiency.

Overview

Semaglutide (e.g., Ozempic, Wegovy) is a GLP-1 receptor agonist used to manage type 2 diabetes. While its active ingredients are well-studied, the role of excipients remains underexplored. We built predictive models to screen FDA-approved excipients for bioactivity potential, offering a computational pathway for formulation optimization.

Objectives

Predict pChEMBL values using machine learning.

Evaluate excipient interactions with the GLP-1 receptor.

Identify promising candidates for injectable and oral drug formulations.

Compare model performance across Random Forest (RF), Support Vector Regression (SVR), and Multi-Layer Perceptron (MLP).


Methodology

Data Sources

Bioactivity data: ChEMBL (CHEMBL1784, filtered to 1,331 compounds).

Excipients data: FDA Inactive Ingredient Guide (IIG).

Molecular Representation: SMILES from PubChem API.


Preprocessing & Feature Engineering

Cleaned and deduplicated datasets using pandas.

Converted SMILES to Morgan fingerprints (2048-bit) using RDKit.

Implemented caching for SMILES lookups.


Modeling Approach

Trained three regressors: Random Forest, SVR, and MLP.

Tuned hyperparameters with RandomizedSearchCV.

Applied 5-fold cross-validation for evaluation.

Assessed models using RMSE, R², and MAE.


Results

Best Performer: Random Forest — balanced precision and low error.

Key Predictions:

Lysine Monohydrate (IV) – pChEMBL: 6.91 (RF)

DSPC (injectable liposome former) – pChEMBL: 8.10 (MLP)

Icodextrine (oral) – pChEMBL: 14.41 (SVR, likely overfitted)



Implications

Computational models can screen excipients for bioactivity, supporting rational formulation design.

Regulatory frameworks could benefit from such predictive tools to assess novel excipients early.

Liposomal agents like DSPC and stabilizers like Lysine Monohydrate show high potential for peptide drug delivery systems.



Technologies & Tools

Languages & Platforms:

Python 3.11.5, Google Colab


Libraries & APIs:

RDKit, pandas, scikit-learn, requests, PubChem API


License

This project is open for academic and non-commercial use. For reproduction or distribution, please contact the author.


---

