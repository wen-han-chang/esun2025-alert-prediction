# 📂 Model — Alert Account Prediction Model

This folder contains the **training and inference pipeline** for the alert account prediction task.  
It consumes features produced by `Preprocess/feature_engineering_timefix.py` and outputs submit-ready CSV files.

---

## 📁 Files

### `model.py`

Main script for **training the model and generating predictions**.

It performs the following steps:

1. **Read feature files** from `../data/`:
   - `features_train.csv`
   - `features_pred.csv`
   - `features_meta.json`
2. **Load feature metadata**:
   - Uses `feature_cols` from `features_meta.json`.
   - If `feature_cols` is empty, it falls back to auto-detect feature columns from `features_train.csv`
     (excluding `acct`, `label`, and `is_unlabeled`).
3. **Train a PU-style LightGBM classifier**:
   - Positive samples: labeled alerts (`label = 1`).
   - Unlabeled samples: non-alert accounts (`label = 0`, `is_unlabeled = 1`).
   - Applies class weights with `GAMMA_FIXED` for unlabeled data.
   - Uses Stratified K-Fold (`N_FOLDS`) with out-of-fold (OOF) predictions.
4. **Apply Platt scaling (Logistic Regression)** on OOF scores:
   - Calibrates raw probabilities to obtain `meta_cal` for both train and test.
5. **Train a Ranker model** (LightGBM) on a middle probability band (`BAND`):
   - Uses only samples whose OOF scores are within the specified quantile range.
   - Ranker outputs `rank_score` for test accounts.
6. **Combine meta and rank scores**:
   - Final score:
     ```python
     final_score = ALPHA * meta_cal + (1 - ALPHA) * rank_score
     ```
7. **Select Top-K accounts** based on `final_score`:
   - `K` is determined from `RATE`, which is derived from the public ACC0.
8. **Write two CSV files** into `../submit/`:
   - `submit_stack_topk.csv`
     - Final submission file containing:
       - `acct`
       - `predict` (0 or 1)
   - `acct_predict_out_stack.csv`
     - Debug / analysis file containing:
       - `acct`
       - `final_score`
       - `meta_cal`
       - `rank_score`

---

## 🔧 How to run

### 1️⃣ Run **full pipeline** from project root

This assumes your project structure is:

- `Preprocess/feature_engineering_timefix.py`
- `Model/model.py`
- `data/` with original CSVs
- `main.py`

Then:

```bash
python main.py
