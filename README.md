[cluster-validation-README.md](https://github.com/user-attachments/files/34033616/cluster-validation-README.md)
# Cluster Validation: CGM Meal Response Analysis

## Overview

This repository documents an unsupervised machine learning project completed for CSE 572: Data Mining as part of my Master of Computer Science program at Arizona State University.

The goal was to investigate whether glucose responses associated with meals form meaningful groups and how closely those groups correspond to recorded carbohydrate quantities. I applied KMeans and DBSCAN clustering to numerical features extracted from continuous glucose monitor (CGM) data, then evaluated the resulting clusters using SSE, entropy, and purity.

## Dataset and Preprocessing

I used CGM and insulin pump records from one patient. Meal events were identified using nonzero carbohydrate entries in the insulin pump data, and I extracted 30-reading CGM windows spanning the period around each meal.

Windows without the expected number of readings or with no valid glucose measurements were excluded. For otherwise complete windows containing some missing glucose measurements, I used pandas interpolation to fill the gaps.

Unlike the supervised classification project, which distinguished Meal from No-Meal windows, this analysis focused only on meal-associated glucose responses.

## Feature Engineering

I extracted eight features from each meal observation window, reusing the time-domain and frequency-domain feature engineering approach developed in the related Machine Model Training project.

The features included time to peak, normalized peak difference, the maximum first and second glucose differences, and the magnitudes and frequencies of the two strongest positive-frequency components from the Fast Fourier Transform (FFT).

Before clustering, I standardized the feature matrix with scikit-learn's `StandardScaler` so that features with larger numerical values would not dominate distance calculations.

## Unsupervised Machine Learning

### KMeans Clustering

I used scikit-learn's `KMeans` algorithm to partition the standardized feature vectors into six clusters. The number of clusters matched the number of carbohydrate categories used in the subsequent validation step. I set `random_state=42` and `n_init=10` for reproducibility.

### DBSCAN Clustering

I also applied DBSCAN, a density-based clustering algorithm that identifies dense groups of observations and can label isolated observations as noise. My implementation used `eps=1.2` and `min_samples=5`.

The code included a conditional refinement procedure: if DBSCAN produced fewer clusters than the target number, I identified the cluster with the largest sum of squared errors and split it using KMeans. Noise observations remained unassigned.

## Cluster Validation

To evaluate the clustering results, I grouped the recorded meal carbohydrate quantities into categories and compared those categories with the clusters identified by each algorithm. The carbohydrate amounts were used for validation rather than as input features for clustering.

I calculated three evaluation metrics:

- **Sum of Squared Errors (SSE):** Measures how compactly observations are grouped around their cluster centers.
- **Entropy:** Measures how mixed the carbohydrate categories are within each cluster; lower values indicate less mixing.
- **Purity:** Measures the proportion belonging to the most common carbohydrate category in each cluster; higher values indicate greater category consistency.

For DBSCAN, observations labeled as noise were excluded from the cluster contingency matrix and SSE calculations. The implementation used the total number of meal windows to weight DBSCAN entropy and purity, including the noise observations in that denominator. This convention should be considered when comparing results with KMeans.

### Results

This analysis compared the compactness of the clusters and their agreement with recorded carbohydrate quantities. It also illustrated a key limitation: features useful for identifying meal activity do not necessarily separate meals by carbohydrate quantity.

*Numerical clustering results and visualizations may be added later.*

## Technologies Used

- **Python:** Data processing and cluster validation
- **pandas:** Timestamp handling, meal-window extraction, and interpolation
- **NumPy:** Feature extraction, FFT, and numerical calculations
- **scikit-learn:** StandardScaler, KMeans, and DBSCAN

## Academic Integrity

This repository documents a project completed for CSE 572: Data Mining at Arizona State University. Original assignment solutions, course datasets, and submission files are not publicly distributed. The purpose of this repository is to summarize the methodology and skills demonstrated by the project.
