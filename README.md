# Predicting a Heart Attack (BRFSS 2022)

> **DATA 1030 Final Project — Brown University**  
> **Author:** Arpit Dang  

This repository contains code and documentation for predicting the likelihood that an individual has **ever had a heart attack** using the 2022 **BRFSS** (Behavioral Risk Factor Surveillance System) survey.  
The project frames the task as a **binary classification** problem with a strong emphasis on **recall**, evaluated primarily with the **F₂ score** (recall-heavy).

---

## 📊 Key Results

- **Dataset (after cleaning):** 246,022 respondents (39 features)  
- **Class imbalance:** ~5.5% positive (“HadHeartAttack = Yes”)  
- **Train/Val/Test split:** 60% / 20% / 20% with **stratification**  
- **Best model:** Logistic Regression  
  - **Best F₂ (single run):** 0.531 (Elastic Net, `l1_ratio=0.5`, `C=10`)  
  - **Mean F₂ (across seeds):** 0.512  
  - **Accuracy:** 83.4%  
- **Baseline F₂:** 0.22 (predicting all positives)  
- **Top predictors (consistent across methods):**
  - HadAngina (angina history)
  - Age category  
  - Sex  
  - General health, prior chest scan, smoking status  

---


---

## 🧮 Data & Features

- **Source:** BRFSS 2022 (U.S. adult survey)  
- **Target:** `HadHeartAttack` ∈ {No, Yes}  
- **Features (39 total):**
  - Continuous (6): scaled with `MinMaxScaler`  
  - Ordinal (6): encoded with `OrdinalEncoder` (explicit order)  
  - Categorical (27):
    - Binary (23): encoded with `OrdinalEncoder` (`No→0`, `Yes→1`)  
    - Non-binary (4): encoded with `OneHotEncoder`

**No missing data** remained after cleaning.  
**Correlations:** max 0.86 (Weight vs BMI) — both retained for clinical interpretability.

---

## ⚙️ Modeling & Training

**Models evaluated:**
- Logistic Regression (L1, L2, Elastic Net)
- Random Forest Classifier
- Support Vector Classifier (trained on 20% of data)
- XGBoost (with `scale_pos_weight=17.31`)

**Hyperparameter grids:**
- **Logistic Regression:**  
  `penalty ∈ {l1, l2, elasticnet(l1_ratio=0.5)}`  
  `C ∈ {1e-3,…,1e2}`, `class_weight='balanced'`
- **Random Forest:**  
  `max_depth ∈ {1,3,10,30,100,300,None}`  
  `max_features ∈ {0.25,0.5,0.75,1.0}`, `class_weight='balanced'`
- **SVC:**  
  `C ∈ {0.01,0.1,1,10,100}`, `gamma ∈ {scale, auto}`, `class_weight='balanced'`
- **XGBoost:**  
  `learning_rate ∈ {1e-3,1e-2,0.1,1,10,100}`,  
  `max_depth ∈ {5,10,30,100,300}`,  
  `scale_pos_weight=17.31`

**Evaluation metrics:**
- Primary: **F₂ score (β=2)**  
- Also tracked: precision, recall, accuracy, PR curves  
- Averaged over **5 random seeds**

---

## 🔎 Explainability

- Used **Permutation Importance**, **Logistic Coefficients**, and **SHAP Values**
- **Key features influencing predictions:**
  - HadAngina (strongest)
  - AgeCategory  
  - Sex  
  - GeneralHealth  
  - SmokerStatus  
- Visualized case-level SHAP force plots for interpretability.

---


