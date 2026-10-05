# Exploration of Spotify tracks dataset - DM1

The project analyses a **Spotify tracks dataset** (15,000 tracks, 24 attributes, 20 music genres) with the full data mining pipeline:

1. Data understanding & preparation
2. Clustering
3. Classification
4. Regression
5. Pattern mining

---

## Pipeline

### 1. Data understanding & preparation
- Described every variable and checked data quality (missing values, redundant variables, correlations).
- Filled missing `time_signature` values using `round(n_beats / n_bars)`.
- Dropped uninformative or redundant columns: `mode`, `popularity_confidence`, `features_duration_ms`, `n_beats`, `n_bars`, `processing`.
- Outliers were analysed with the IQR method but **kept**, because removing them heavily unbalanced some genres (e.g. *sleep*, *j-dance*).

### 2. Clustering
Data scaled with **Min-Max** scaling.

| Algorithm | Main setting | Silhouette |
|---|---|---|
| K-Means | k = 4 | 0.21 |
| DBSCAN | eps = 0.67 | 0.35 |
| Hierarchical | Ward + connectivity | 0.189 |

- **Best choice: K-Means.** It gave the best balance of visual separation, genre distribution and score.
- DBSCAN produced one giant cluster (curse of dimensionality), so its higher silhouette is misleading.
- The 4 clusters are mainly separated by **energy** and **instrumentalness**.

### 3. Classification
Two targets were classified with **KNN**, **Naïve Bayes** and **Decision Tree** (grid/random search, stratified cross-validation):

| Model | Accuracy (genre, 20 classes) | Accuracy (K-Means labels) |
|---|---|---|
| **KNN** | **0.418** | **0.973** |
| Naïve Bayes | 0.220 | 0.574 |
| Decision Tree | 0.382 | 0.967 |

- Predicting the genre is hard, as many genres share similar audio features.
- Predicting the cluster labels works very well with KNN and Decision Tree.

### 4. Regression
Goal: predict track **popularity** (Linear, Ridge, Lasso, KNN, Decision Tree).

| Setting | Best model | R² |
|---|---|---|
| Univariate (`instrumentalness`) | KNN | 0.126 |
| Multivariate (continuous features) | Decision Tree | 0.198 |

Predictive power is low overall, since key information (e.g. play counts, release dates) is not in the dataset.

### 5. Pattern mining
- Continuous variables discretised into *low / medium / high*.
- Frequent itemsets extracted with **Apriori** and **FP-Growth**.
- **830 association rules** found (support 10, confidence 50), mostly with confidence > 0.60 and lift > 1.8.
- Rules for popularity: low instrumentalness is linked to high popularity, and vice versa.
- Using the rules to fill missing `popularity_confidence` values did **not** work (low support and confidence).

---

## Conclusions

- The most informative features across all tasks are **energy, loudness, instrumentalness and acousticness**.
- Tracks can be grouped into **4 clusters**, from low energy / high instrumentalness (*sleep*, *study*) to high energy / low instrumentalness (*techno*, *black-metal*).
- **KNN** is the best classifier overall, while **Naïve Bayes** performs worst.

---

## Authors

Javier Alejandro Borges Legrottaglie, Nicola Pastorelli, Marco Sanna