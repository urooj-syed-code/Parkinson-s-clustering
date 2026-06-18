# Clustering Parkinson’s Telemonitoring Voice Measurements

This project applies unsupervised clustering techniques to Parkinson’s telemonitoring voice data to identify meaningful acoustic profiles from biomedical voice measurements.

The analysis was completed for a graduate-level Data Mining and Predictive Analytics assignment using **Orange Canvas**. The main goal was to determine whether Parkinson’s voice recordings naturally group into distinct acoustic profiles and whether those groups show differences in UPDRS symptom severity scores.

## Project Overview

Parkinson’s disease can affect speech production and voice quality. This project uses biomedical voice measurements from Parkinson’s patients to explore whether recordings can be grouped based on acoustic characteristics such as pitch instability, amplitude variation, vocal noise, and signal complexity.

The project compares:

* **K-Means Clustering**
* **Hierarchical Clustering**
* **Principal Component Analysis (PCA)** for visualization

## Research Question

Can biomedical voice measurements from Parkinson’s telemonitoring data be clustered into distinct acoustic profiles, and do those clusters show differences in UPDRS symptom severity scores?

## Dataset

**Dataset:** Parkinson’s Telemonitoring Dataset
**Source:** UCI Machine Learning Repository
**Rows:** 5,875 voice recording sessions
**Patients:** 42 individuals with Parkinson’s disease
**Voice features used:** 16 biomedical voice measurements

Each row represents a voice recording session, not a unique patient. The same patient may appear multiple times across the dataset.

Dataset link:
https://archive.ics.uci.edu/dataset/189/parkinsons+telemonitoring

## Features Used for Clustering

Only biomedical voice measurement features were used as clustering inputs:

* `Jitter(%)`
* `Jitter(Abs)`
* `Jitter:RAP`
* `Jitter:PPQ5`
* `Jitter:DDP`
* `Shimmer`
* `Shimmer(dB)`
* `Shimmer:APQ3`
* `Shimmer:APQ5`
* `Shimmer:APQ11`
* `Shimmer:DDA`
* `NHR`
* `HNR`
* `RPDE`
* `DFA`
* `PPE`

## Meta Variables

The following variables were kept as metadata for interpretation but were **not used** to create the clusters:

* `subject#`
* `age`
* `sex`
* `test_time`
* `motor_UPDRS`
* `total_UPDRS`

UPDRS scores were used only after clustering to compare whether the discovered acoustic groups showed differences in symptom severity.

## Tools Used

* **Orange Canvas** for clustering, preprocessing, PCA, visualizations, and workflow design
* **PowerPoint** for the final presentation
* **GitHub** for project documentation and file organization

## Orange Workflow

The final Orange workflow used one shared preprocessing path before branching into separate analysis methods:

```text
File
→ Select Columns
→ Preprocess
→ Data Sampler
   → K-Means
   → PCA
   → Distances → Hierarchical Clustering
```

This ensured that K-Means, PCA, and Hierarchical Clustering all used the same selected, standardized, and sampled dataset.

## Preprocessing

The preprocessing steps included:

1. Loading the Parkinson’s telemonitoring dataset into Orange.
2. Selecting the 16 biomedical voice features as clustering inputs.
3. Keeping patient information and UPDRS scores as metadata.
4. Standardizing the voice features using Z-score normalization.
5. Sampling 4,000 records using Orange’s Data Sampler.

A 4,000-row sample was used because Orange’s Silhouette Plot does not compute silhouette scores for datasets above 5,000 samples. The original dataset had 5,875 rows.

## K-Means Clustering

K-Means was tested with cluster values from `k = 2` through `k = 6`.

| Number of Clusters | Silhouette Score |
| -----------------: | ---------------: |
|                  2 |            0.746 |
|                  3 |            0.366 |
|                  4 |            0.300 |
|                  5 |            0.232 |
|                  6 |            0.241 |

The best result was **k = 2**, with a silhouette score of **0.746**.

This indicated that the dataset separated most clearly into two acoustic profiles.

## Cluster Interpretation

### Cluster C1: Higher Acoustic Impairment Profile

Cluster C1 showed:

* Higher Jitter values
* Higher Shimmer values
* Higher NHR
* Higher RPDE
* Higher PPE
* Lower HNR

This suggests greater pitch instability, amplitude variation, vocal noise, signal complexity, and reduced vocal clarity.

### Cluster C2: Lower Acoustic Impairment Profile

Cluster C2 showed:

* Lower Jitter values
* Lower Shimmer values
* Lower NHR
* Lower RPDE
* Lower PPE
* Higher HNR

This suggests more stable voice patterns, lower noise, and better vocal clarity.

## UPDRS Comparison

After clustering, UPDRS scores were compared as metadata variables.

| Cluster                         | Mean motor_UPDRS | Mean total_UPDRS |
| ------------------------------- | ---------------: | ---------------: |
| C2 — Lower Acoustic Impairment  |            21.20 |            28.89 |
| C1 — Higher Acoustic Impairment |            23.55 |            31.08 |

Cluster C1 had higher average motor_UPDRS and total_UPDRS scores than Cluster C2. This suggests that the acoustic clusters may reflect differences in Parkinson’s symptom severity.

However, this relationship is exploratory and should not be interpreted as diagnostic.

## PCA Visualization

Principal Component Analysis was used to reduce the 16 voice features into two principal components for visualization.

The first two principal components explained approximately **80.8% of the variance** in the dataset.

This supported the visual interpretation of the two acoustic profiles found through clustering.

## Hierarchical Clustering

Hierarchical Clustering was used as a second clustering method to compare against K-Means.

The Orange workflow used:

```text
Data Sampler → Distances → Hierarchical Clustering
```

The dendrogram supported a small number of dominant groupings, which aligned with the K-Means result of two primary acoustic profiles.

## Key Findings

* K-Means performed best with **2 clusters**.
* The best silhouette score was **0.746**.
* PCA showed that two principal components explained approximately **80.8%** of variance.
* Hierarchical Clustering supported a small number of dominant groupings.
* The two final cluster labels were:

  * **Higher Acoustic Impairment Profile**
  * **Lower Acoustic Impairment Profile**
* The higher acoustic impairment cluster also showed higher average UPDRS scores.

## Limitations

Several limitations should be considered:

* The dataset contains repeated recordings from the same patients, so rows are not fully independent.
* A 4,000-row sample was used because of Orange’s silhouette score limitation.
* Acoustic impairment likely exists on a continuum rather than only two discrete groups.
* UPDRS differences across clusters are associations, not proof of causation.
* The results should be interpreted as exploratory, not diagnostic.

## Future Work

Possible future improvements include:

* Aggregating recordings at the patient level
* Testing additional clustering methods such as DBSCAN or Gaussian Mixture Models
* Tracking cluster changes over time
* Analyzing PCA loadings to identify the most influential voice features
* Exploring cloud-based analytics workflows for larger healthcare datasets

## Repository Contents

Suggested repository structure:

```text
.
├── README.md
├── data/
│   └── parkinsons_updrs.data
├── orange-workflows/
│   └── parkinsons_clustering_master_workflow.ows
├── presentation/
│   └── parkinsons_final.pptx
├── screenshots/
│   ├── orange_workflow.png
│   ├── kmeans_silhouette.png
│   ├── kmeans_boxplots.png
│   ├── pca_scatterplot.png
│   └── hierarchical_dendrogram.png
└── documentation/
    └── project_summary.docx
```
