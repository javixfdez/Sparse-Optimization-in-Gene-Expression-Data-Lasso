# Sparse-Optimization-in-Gene-Expression-Data-Lasso

This project implements a **sparse optimization** approach using Lasso Regression (L1 regularization) to perform robust feature selection on a high-dimensional gene expression dataset (LUSC cohort, $P > 24,000$ genes).

The primary objective is to select a minimal, interpretable subset of genes that maintains high predictive accuracy.

The data was acquired by the public kaggle dataset "TCGA - LUSC | Lung Cancer Gene Expression Dataset" from https://www.kaggle.com/datasets/noepinefrin/tcga-lusc-lung-cell-squamous-carcinoma-gene-exp/data . 

Dataset consists of 551 patients, samples, each with 56970 differetn transcripts (expressed genes). 

Lung Cell Squamos Carcinoma is a cancer that occurs in lungs. Detecting and predicting it by Machine Learning is a clear challenge that could be very helpful.

---

## Methodology: Ridge Baseline vs. Lasso Solution

The project uses a two-model comparison to validate the trade-off between dense performance and sparsity.

### 1. Dense Baseline: Ridge Regression (L2)

| Metric | Result | Interpretation |
| :--- | :--- | :--- |
| **Optimal AUC** | $\approx 1.0000$ | Confirms a strong predictive signal exists. |
| **Non-Zero Coefs** | $24,763$ | Retains all genes (dense model). |
| **Top Coefs** | $\approx 0.0006$ | Weights are spread democratically across all features. |

**Coefficient Insight:** Ridge coefficients are tiny and nearly uniform (e.g., SPP1: 0.000620, HBA1: 0.000553) . This confirms the signal is collective but provides no meaningful gene ranking.

### 2. Sparse Solution: Lasso Regression (L1)

| Metric | Result | Interpretation |
| :--- | :--- | :--- |
| **Optimal AUC** | $\approx 1.0000$ | Maintains maximum performance achieved by the dense baseline. |
| **Non-Zero Coefs** | $\approx 95$ | **Successful Sparsity Achieved** (99.62% of features set to zero). |
| **Top Coefs** | Up to $0.0601$ | Weights are concentrated on a few, highly influential features. |

**Lasso achieved the core goal by finding a model that is both highly predictive and highly interpretable.**

---

##  3. Key Findings: The Top Lasso Genes

The following genes are the **sparse core** of the predictive model, validated across the cross-validation and bootstrap runs. Their concentrated coefficient values (up to $\approx 0.06$) show their critical, individual influence.

| Gene Name | Coefficient (Value) | Association |
| :--- | :--- | :--- |
| **RS1** | **0.060168** | Single strongest **Activator** (positive correlation with target condition). |
| **MIR3945HG** | 0.025671 | High positive predictor. |
| **LINC00702** | 0.016630 | Strong positive predictor. |
| **SPP1** | -0.015327 | Most influential **Repressor** (negative coefficient in the final series). |
| **MYRIP** | 0.012584 | Strong positive predictor. |
| **MYBL2** | -0.011655 | Strong negative repressor. |
| **DSP** | -0.011527 | Strong negative repressor. |
| **HMGA1** | -0.008613 | Notable negative repressor. |

*(The complete, sparse set is saved in `final_lasso_genes.csv`)*

---

##  4. Execution Guide

The project must be executed in order:

### A. Preprocessing (`preprocess.ipynb`)

1. **Filtering:** Removes genes expressed at $< 1$ CPM in $< 10\%$ of samples.
2. **Transformation:** Applies $\log_2(\text{CPM} + 1)$.
3. **Standardization:** Scales all 24,763 remaining features to $\mu=0, \sigma=1$.

### B. Modeling (`modelling.ipynb`)

1. **Baseline:** Runs **DummyClassifier** and **RidgeCV** to set performance floor and ceiling.
2. **LassoCV:** Trains the sparse model with increased `max_iter` for high-dimensional convergence and built-in cross-validation.
3. **Output:** Generates the final metrics and extracts the $\approx 95$ non-zero coefficients.

### C. License

This project is released under the **MIT License**.
