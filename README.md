# Predictive Nephrotoxicity Screening in Small Molecules
### A Multi-Model Cheminformatics Framework with Feature Selection & Tree-SHAP Explainability

---

## 📌 Project Overview
Nephrotoxicity remains a primary driver of high clinical attrition rates, accounting for up to 25% of late-stage drug pipeline failures. Because the kidney systematically concentrates and reabsorbs filtered xenobiotics, preclinical *in silico* safety filters are vital to eliminate high-risk structures before expensive *in vitro* screening.

This repository hosts an end-to-end computational toxicology pipeline developed during my Bachelor of Pharmacy capstone research under the mentorship of **Prof. Rajnish Kumar** at the **Department of Chemical Engineering & Technology, Indian Institute of Technology (Banaras Hindu University), Varanasi**.

The framework ingests raw chemical notation (SMILES), computes structural and physical-chemical molecular features using RDKit, optimizes data dimensions via information theory, balances class spaces, and benchmarks ensemble and deep architectures. Model outputs are completely mapped using Game-Theoretic Explainable AI (**Tree-SHAP**) to ground algorithmic toxicity scores in interpretable biochemical properties.

---

## 📊 Dataset Volumetrics & Structure
The model is trained, validated, and tested on a curated, non-overlapping dataset retrieved directly from the repository's main stream:
* **Total Workspace Capacity:** 565 Small Molecules
* **Clinical Class Distribution:** Highly balanced baseline with **287 Toxic (`1`)** vs. **278 Safe (`0`)** compounds.
* **Evaluation Partitions:** Divided via a randomized `train_test_split` routine into:
  * **Train Set:** 452 compounds (80%) used for feature selection and hyperparameter optimization.
  * **Test Set:** 113 compounds (20%) reserved exclusively as an unseen validation partition.

---

## 🧬 Feature Engineering & Pipeline Architecture
The chemical feature space is dynamically engineered directly from string SMILES formats through a unified multi-stage pipeline:

1. **Physicochemical Descriptors:** Computation of spatial, thermodynamic, and surface properties including Molecular Weight ($MW$), octanol-water partition coefficients ($LogP$), Topological Polar Surface Area ($TPSA$), and structural charge indices via RDKit `Descriptors` module.
2. **Circular Structural Fingerprints:** Vectorization of 2048-bit Morgan Circular Fingerprints (configured with a structural Radius of 2, equivalent to ECFP4) to extract local atomic environments.
3. **Entropy-Based Feature Selection:** Mutual Information (`mutual_info_classif`) entropy filtering is executed across the entire tabular array to measure feature dependency, pruning the matrix noise down to the **Top 300 critical features**.
4. **Partition Over-Sampling:** Synthetic Minority Over-sampling Technique (`SMOTE`) is applied inside an isolated `ImbPipeline` framework to protect learning loops against minor localized sample gaps.

---

## 📈 Cross-Model Tournament & Performance Verification
Architectures were cross-optimized using a **5-Fold Stratified Cross-Validation (`StratifiedKFold`)** grid search (`GridSearchCV`). The final model evaluation on the independent test split reveals **XGBoost** outperforming traditional Random Forest and Deep Multi-Layer Perceptron variants across all critical clinical screening criteria:

| Model Architecture | Test Accuracy | Test ROC-AUC | Test PR-AUC | Test F1-Score |
| :--- | :---: | :---: | :---: | :---: |
| **XGBoost (Winner)** | **0.814159** | **0.898113** | **0.880308** | **0.810811** |
| Random Forest | 0.787611 | 0.875943 | 0.869831 | 0.773585 |
| Deep Neural Network (DNN) | 0.699115 | 0.776415 | 0.751778 | 0.685185 |

### 🔍 Statistical Validation Summary:
To guarantee absolute architectural stability on variable structural chemistry, the selected champion XGBoost pipeline was evaluated under standard cross-validation loops, producing a **Mean Stratified CV ROC-AUC score of 0.843 ± 0.046** (Reported as: `0.843 ± 0.046`).

---

## 🧠 Model Explainability (Tree-SHAP)
Instead of relying on black-box classifications, this framework integrates **Tree-SHAP** (Shapley Additive exPlanations) to decipher the mathematical logic driving the XGBoost model's evaluations:

* **Electronic Graph Configurations:** Physical-chemical descriptors measuring surface charges and geometric shapes serve as vital global properties, showing that distinct surface charge variations dictate renal tissue affinity or reabsorption behavior.
* **Circular Substructures:** Local structural fragments mapped within the 2048-bit circular array generate high positive Shapley values, providing reproducible, data-driven toxicophore evidence that shifts outputs toward toxic risk boundaries.

![SHAP Summary Plot](https://github.com/sakshihulke11-oss/KidneyTox-QSAR/blob/main/SHAP_Summary%20(7).png)

---

## 🚀 Virtual Screening Production Engine
The pipeline compiles into a functional, standalone screening module that parses raw chemical string inputs into immediate toxicity risk probabilities:

```python
# Production Screening Test Configuration
from modeling_pipeline import live_screening_engine

# Input Query String: Aspirin
target_smiles = "CC(=O)OC1=CC=CC=C1C(=O)O" 

# Execute extraction and model inference
parsed_payload = live_screening_engine(target_smiles)
live_prob_score = xgb_best.predict_proba(parsed_payload)[0][1]

print(f"Computed Predictive Nephrotoxicity Probability: {live_prob_score:.4f}")
# Output: Computed Predictive Nephrotoxicity Probability: 0.0130

print(f"Diagnostic Action Verdict: {'[ NEPHROTOXIC ] ❌' if live_prob_score >= 0.5 else '[ NON-NEPHROTOXIC ] ✅'}")
# Output: Diagnostic Action Verdict: [ NON-NEPHROTOXIC ] ✅


├── Nephrotoxicity_Smallmolecules_dataset.csv  # Curated molecular training reference data
├── Nephrotoxicity_smallcompounds.ipynb        # Complete model optimization development notebook
├── SHAP_Summary.png                           # Tree-SHAP global feature risk visualization
├── README.md                                  # Repository documentation portfolio profile
└── requirements.txt                           # Absolute dependency runtime parameters
