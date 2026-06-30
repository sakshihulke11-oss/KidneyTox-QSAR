# KidneyTox-QSAR
An explainable Machine Learning pipeline for small-molecule drug nephrotoxicity prediction using RDKit and Gradient Boosting.
# KidneyTox-QSAR: Explainable Machine Learning Pipeline for Nephrotoxicity Prediction

An automated *in silico* Quantitative Structure-Activity Relationship (QSAR) classification framework designed to screen drug candidates for chemical nephrotoxicity during early pre-clinical development. 

This project was developed under the technical framework of the **IIT (BHU) Varanasi · NPTEL Summer Internship 2026**.

## 📌 Core Architecture
The predictive pipeline operates across 6 distinct stages:
1. **Data Curation:** Ingestion of the `Kidney Tox_v1.0` cohort mapping 565 parsed rows of organic compound matrices.
2. **Hybrid Featurization:** Simultaneous extraction of 2D global physicochemical properties and 1024-bit Morgan topological fingerprints (ECFP4 equivalent) via RDKit.
3. **Information Gain Filtering:** Dimensionality reduction using Mutual Information Entropy (`mutual_info_classif`) to compress over 1,100 features down to the **Top 35 highest-entropy parameters**.
4. **Ensemble Modeling:** Execution of a regularized Gradient Boosting Machine (GBM) Classifier.
5. **Applicability Domain Guardrail:** Structural anomaly filtering via the **Hat Matrix Leverage Equation** at a strict **98.3rd percentile threshold** to completely block low-confidence guessing.
6. **XAI Interpretability:** Extraction of dataset-wide Gini Feature Importance vectors to identify toxicity driving forces.

## 📊 Performance Vector
* **Baseline Framework Accuracy:** ~56.00% (Raw unoptimized data vectors)
* **Production Pipeline Accuracy:** **74.77% Validated Accuracy** (Strictly within the safe Applicability Domain boundary)
* **Identified Chemical Drivers:** Molecular Weight (`ExactMolWt`) and Topological Polar Surface Area (`TPSA`) show the highest structural split coefficients.

## ⚙️ Model Hyperparameters & Configuration
These are the exact execution settings configured inside the Gradient Boosting Machine (GBM) model to prevent overfitting on noisy data:
* `n_estimators`: 150 (Total number of sequential decision trees built)
* `learning_rate`: 0.05 (Step-size shrinkage used to prevent over-indexing on noise)
* `max_depth`: 3 (Limits the depth of each individual tree to keep the model simple)
* `subsample`: 0.80 (Uses 80% of rows at random per tree to create stable generalization)
* `random_state`: 42 (Locks the random seed so results are 100% reproducible)

## ⚠️ Pipeline Validation Gaps & Limitations
While the pipeline successfully increases predictive accuracy within the safe boundary, the following methodological gaps are acknowledged as open limitations:

1. **Matrix Partition Gaps:** The model relies strictly on the dataset's native pre-allocated Train/Test columns to maintain a direct benchmark comparison with the original literature. A structural Bemis-Murcko scaffold split or a 5-fold stratified cross-validation loop was not implemented in this baseline version.
2. **Missing Probability Threshold Metrics:** Evaluation is restricted to flat classification parameters (Accuracy, Precision, Recall, F1-score). Advanced threshold-independent evaluations—specifically ROC-AUC, PR-AUC, and Brier probability calibration curves—represent a current validation gap.
3. **Localized Interpretability Limits:** Model explainability relies on global Gradient Boosting Gini split counts. Localized, per-compound feature tracking (such as SHAP waterfall frameworks) is not built into the current code architecture.

## 📚 Academic & Literture References

1. **Core Benchmark Dataset Framework:** Amin, R., Kumar, R., et al. (2026). *KidneyTox_v1.0: An Open-Access Benchmarking Dataset and Machine Learning Framework for Small-Molecule Drug-Induced Nephrotoxicity Prediction*. **Scientific Reports**, 16(1), 4105-4118.
   
2. **Original Clinical Cohort Mapping:** Shi, W., et al. (2021). *Predicting Drug-Induced Severe Kidney Injury Using Clinical Adverse Drug Reaction Profiles and Molecular Descriptors*. **Journal of Cheminformatics**, 13(4), 112-125.
   
3. **Cheminformatics & Feature Engineering Toolkits:** Landrum, G. (2024). *RDKit: Open-Source Cheminformatics Software for Molecular Descriptor Generation and Topological Fingerprinting*. Software available at: https://www.rdkit.org.
   
4. **Validation Boundary Principles (OECD Guidelines):** OECD (Organisation for Economic Co-operation and Development). (2007). *Guidance Document on the Validation of (Quantitative) Structure-Activity Relationship [(Q)SAR] Models*. OECD Environment Health and Safety Publications, Series on Testing and Assessment, No. 69.

