# Anomaly-Based DDoS Detection: A Comparative Study

A systematic, comparative evaluation of four anomaly-detection paradigms — **Z-Score**, **Isolation Forest**, **One-Class SVM (OCSVM)**, and **Local Outlier Factor (LOF)** — for detecting Distributed Denial-of-Service (DDoS) traffic in flow-level network data.

This project was completed as part of a technical internship at the **Defence Research and Development Organisation (DRDO)**.

---

## Overview

Distributed Denial-of-Service (DDoS) attacks remain one of the most disruptive threats to modern network infrastructure, exploiting the *availability* pillar of the CIA triad by overwhelming targets with coordinated traffic. Signature-based Intrusion Detection Systems (IDS) are effective against known attacks but cannot identify zero-day or polymorphic variants — motivating anomaly-based detection, which learns a model of "normal" network behavior and flags significant deviations from it.

This project benchmarks four anomaly-detection techniques, spanning distinct algorithmic philosophies:

| Method | Type | Approach |
|---|---|---|
| **Z-Score** | Statistical (univariate) | Flags points that deviate significantly from the mean, assuming Gaussian-distributed features |
| **Isolation Forest** | Ensemble, partition-based | Isolates anomalies via random recursive partitioning of the feature space |
| **One-Class SVM** | Kernel-based, boundary | Learns a non-linear decision boundary (RBF kernel) enclosing normal traffic |
| **Local Outlier Factor (LOF)** | Density-based | Scores points by the local density deviation relative to their neighbors |

## Dataset

- **Source:** [CICIDS2017](https://www.unb.ca/cic/datasets/ids-2017.html) benchmark dataset — *Friday-WorkingHours-Afternoon-DDoS* subset
- **Features:** Flow-level statistical descriptors extracted via **CICFlowMeter** (e.g., flow duration, packet counts, inter-arrival times, TCP flag counts)
- **Preprocessing:** Handling of missing/infinite values, duplicate flow removal, and feature scaling prior to model development

## Methodology

1. **Preprocessing pipeline** — cleaning, deduplication, and scaling of raw flow data
2. **Baseline modeling** — univariate Z-Score thresholding across multiple threshold levels
3. **Machine learning models** — Isolation Forest, One-Class SVM, and LOF, each subjected to a structured hyperparameter search (e.g., contamination, `n_estimators`, `nu`, `gamma`, `n_neighbors`)
4. **Evaluation** — Precision, Recall, F1-score, overall accuracy, confusion matrix, False Positive Rate, Benign Accuracy, DDoS Detection Rate, and training/prediction time

## Results

| Model | F1-Score | DDoS Detection Rate |
|---|---|---|
| Z-Score (baseline) | 0.29 | Low |
| Isolation Forest | 0.84 | High |
| One-Class SVM | 0.97 | High |
| **Local Outlier Factor** | **0.99** | **~99.90%** |

**Key findings:**
- The **Z-Score baseline** proved unsuitable as a standalone detector due to its univariate, axis-aligned formulation, retaining value only as a computationally negligible point of comparison.
- All three machine learning methods substantially outperformed the statistical baseline once tuned, confirming that **multivariate modeling of joint feature structure** is necessary for effective DDoS detection on this dataset.
- **Local Outlier Factor (LOF)** achieved the strongest overall performance — the highest F1-score and DDoS Detection Rate combined with the lowest False Positive Rate among the ML methods — consistent with flooding-style DDoS traffic forming a locally distinguishable population well suited to density-based detection.
- **One-Class SVM** achieved closely comparable detection accuracy, at a considerably higher computational cost during training.
- **Isolation Forest** offered the most favorable computational scalability, at some cost to detection performance relative to LOF and OCSVM.

## Repository Structure

```
├── figures                # Evaluation outputs, plots, and confusion matrices
├── notebooks/             # Exploratory analysis and model experiments               
└── README.md
```

> Update this section to match your actual folder layout.

## Tech Stack

- **Python** — pandas, NumPy, scikit-learn
- **CICFlowMeter** — flow feature extraction
- **Matplotlib / Seaborn** — visualization (ROC/PR curves, hyperparameter heatmaps, PCA projections, KDE score distributions)

## Getting Started

```bash
git clone https://github.com/yatharthagarwal0620-art/network-anomaly-detection.git
cd network-anomaly-detection
pip install -r requirements.txt
```

Run the notebooks in `notebooks/` sequentially, or execute the pipeline scripts in `src/` directly.

## Acknowledgements

This work was carried out as part of a technical internship at DRDO, with the Z-Score and OCSVM experimental sections developed by Yatharth Agarwal and Isolation Forest and Local Outlier Factor experimental sections developed by Anshika Chauhan

## License

This project is licensed under the MIT License.
