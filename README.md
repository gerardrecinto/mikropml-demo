# mikropml Demo

Demonstration of [mikropml](https://github.com/SchlossLab/mikropml) — an R package that standardizes supervised ML classification pipelines with rigorous held-out test set handling, cross-validation, and multi-model comparison.

![R](https://img.shields.io/badge/R-4.x-276DC3?logo=r&logoColor=white)
![mikropml](https://img.shields.io/badge/mikropml-1.x-brightgreen)
![License: MIT](https://img.shields.io/badge/License-MIT-22c55e)

---

## What mikropml does

A common ML mistake is letting the test set influence preprocessing. mikropml enforces the correct order:

1. **Hold out test set first** — before any scaling, imputation, or feature selection
2. **Preprocess training set only** — scaling and transformations fit to training data, then applied to test
3. **Cross-validate** on training set with grouped k-fold (prevents data leakage across replicates)
4. **Train and evaluate** logistic regression, random forest, SVM, gradient boosting, and neural net
5. **Report AUROC, sensitivity, specificity** per model on the held-out test set

---

## Quick start

```r
install.packages("mikropml")
library(mikropml)

# Built-in dataset: OTU table + diagnosis labels
results <- run_ml(
  otu_mini_bin,
  method          = "glmnet",
  outcome_colname = "dx",
  find_hp         = TRUE,
  seed            = 2019
)

results$performance
plot_model_performance(results$performance)
```

---

## Multi-model comparison

```r
library(future)
plan(multisession)

models <- c("glmnet", "rf", "svmRadial", "xgbTree")

all_results <- purrr::map(models, function(m) {
  run_ml(otu_mini_bin, m, outcome_colname = "dx", seed = 2019)
})

perf <- purrr::map_dfr(all_results, "performance")
plot_model_performance(perf)
```

---

## Requirements

- R 4.x
- `mikropml` — `install.packages("mikropml")`
- `future` + `purrr` for parallel multi-model runs
- `ggplot2` for visualization

---

## References

- Topçuoğlu et al. (2021). [A framework for effective application of machine learning to microbiome-based classification problems.](https://doi.org/10.1128/mBio.00434-21) *mBio*.
- [mikropml docs](https://www.schlosslab.org/mikropml/)
