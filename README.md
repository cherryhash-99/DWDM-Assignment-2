# Data Mining & Data Warehousing – Assignment 2 (Orange / Weka)

Same 9 experiments and same workflow structure as the reference assignment, but each one uses a **different dataset** (the reference used Titanic and Iris).

| # | Experiment | Dataset (new) | Original |
|---|-----------|---------------|----------|
| 1 | Data Cleaning | Palmer Penguins (`penguins.csv`) | Titanic |
| 2 | Data Reduction | Wine (`wine.csv`) | Titanic |
| 3 | Data Transformation | Penguins (cleaned) | Titanic |
| 4 | Apriori / Association Rules | Groceries baskets (`groceries_baskets.csv`) | Market basket |
| 5 | Statistical Analysis | Penguins | Titanic |
| 6 | Regression | Auto-MPG (`mpg.csv`) | – |
| 7 | Outlier Analysis | Auto-MPG | – |
| 8 | k-Means (Orange) | Wine | Iris |
| 9 | Simple K-Means (Weka, ARFF) | `wine.arff` | Iris ARFF |

Folders: `datasets/` (input data), `workflows/` (Orange `.ows`), `output.png/` (result images).

---
## Experiment 1: Data Cleaning Using Orange
**Aim:** Clean missing values in the Penguins dataset.
**Dataset:** `penguins.csv` – 344 instances, 7 features. 19 missing cells: 2 each in bill_length_mm, bill_depth_mm, flipper_length_mm, body_mass_g and 11 in sex.
**Workflow:** `File → Impute → Data Table`
**Procedure:** Load the file with File; connect to Impute and choose *Average / Most frequent*; connect to Data Table to inspect.
**Output:** Numeric gaps were filled with column means (bill_length 43.92, bill_depth 17.15, flipper 200.92, body_mass 4201.75); the categorical `sex` gaps with the most frequent value (MALE). Missing cells afterwards: 0.
**Result:** Data cleaning completed. Image: `output.png/01_Data_Cleaning_OUTPUT.png`

## Experiment 2: Data Reduction
**Aim:** Reduce the dimensionality of the Wine dataset (13 features).
**Dataset:** `wine.csv` – 178 instances, 13 numeric features, class `cultivar`.
**Workflow:** `File → PCA → Scatter Plot`, `File → Rank → Data Table`
**Procedure:** Standardize features, run PCA and read the explained variance; use Rank to score features against the class.
**Output:** PC1 explains 36.2 %, PC2 19.2 %, PC3 11.1 %; the first 3 PCs cover 66.5 % and 10 PCs are needed for 95 %. Top-ranked features: flavanoids, proline, od280/od315, alcohol, color_intensity.
**Result:** 13 features can be reduced to a few components (or the top-ranked features) with little information loss. Image: `02_Data_Reduction_OUTPUT.png`

## Experiment 3: Data Transformation
**Aim:** Apply normalization, standardization, discretization and continuization.
**Dataset:** Penguins (after imputation).
**Workflow:** `File → Impute → Normalize → Discretize → Continuize → Data Table`
**Output:** body_mass_g min-max scaled to [0, 1]; z-score gives mean 0, std 1; bill_length_mm split into 3 equal-width bins (edges 32.1 / 41.27 / 50.43 / 59.6) – Short 127, Medium 173, Long 44; `sex` one-hot encoded.
**Result:** Data transformed and ready for mining. Image: `03_Data_Transformation_OUTPUT.png`

## Experiment 4: Apriori Association Rules
**Aim:** Find frequent itemsets and association rules in market-basket data.
**Dataset:** `groceries_baskets.csv` (9,835 baskets, 171 items; one-hot copy `groceries_onehot.csv`).
**Workflow:** `File → Frequent Itemsets → Association Rules` (needs the Orange *Associate* add-on).
**Parameters:** min support 1 %, min confidence 30 %.
**Output:** 332 frequent itemsets and 125 rules. Most frequent items: whole milk (25.6 %), other vegetables (19.3 %), rolls/buns (18.4 %). Strongest rule by lift: {citrus fruit, other vegetables} → root vegetables (support 0.010, confidence 0.359, lift 3.295). Full list: `datasets/groceries_rules_output.csv`.
**Result:** Rules with lift > 1 show items bought together more often than by chance. Image: `04_Apriori_Association_Rules_OUTPUT.png`

## Experiment 5: Statistical Analysis
**Aim:** Summarise and relate the Penguins variables.
**Workflow:** `File → Feature Statistics / Correlations / Box Plot`
**Output:** Mean body mass 4201.75 g (median 4050, std 799.6); mean flipper 200.92 mm. Flipper length vs body mass correlation r = 0.871; bill length vs bill depth r = −0.235. One-way ANOVA of body mass across species: F = 338.6, p < 0.001.
**Result:** Body mass differs significantly between species and is strongly tied to flipper length. Image: `05_Statistical_Analysis_OUTPUT.png`

## Experiment 6: Regression
**Aim:** Predict fuel efficiency (mpg) with linear regression.
**Dataset:** `mpg.csv` – 398 cars (6 missing horsepower values mean-imputed); 80/20 split (318 train / 80 test).
**Workflow:** `File → Linear Regression → Test and Score`
**Output:** Simple (mpg ~ weight): R² = 0.723, RMSE 3.859, MAE 3.118. Multiple (weight, horsepower, displacement, model_year): R² = 0.825, RMSE 3.072, MAE 2.455. Newer model years raise mpg (+0.80/yr); weight lowers it.
**Result:** The multiple model explains more variance than the single-feature model. Image: `06_Regression_OUTPUT.png`

## Experiment 7: Outlier Analysis
**Aim:** Detect outliers in Auto-MPG.
**Workflow:** `File → Outliers / Box Plot → Scatter Plot`
**Output:** IQR rule flags 11 horsepower, 7 acceleration, 1 mpg and 0 weight outliers; |Z| > 3 flags 5 horsepower and 2 acceleration. Covariance-estimator (contamination 5 %) flags 20 of 398 cars.
**Result:** Outliers are mainly high-horsepower cars and extreme acceleration values. Image: `07_Outlier_Analysis_OUTPUT.png`

## Experiment 8: k-Means Clustering (Orange)
**Aim:** Cluster the Wine data without using the class label.
**Workflow:** `File → k-Means → Silhouette Plot / Scatter Plot`
**Output:** Silhouette is highest at k = 3 (0.285) compared with k = 2 (0.268) and k = 4 (0.246). Cluster sizes 65 / 51 / 62 (within-cluster SS 1277.9); only 6 of 178 wines fall outside the cluster that matches their true cultivar.
**Result:** k-Means recovers the three cultivars almost perfectly. Image: `08_KMeans_Wine_OUTPUT.png`

## Experiment 9: Simple K-Means in Weka (ARFF)
**Aim:** Run SimpleKMeans on an ARFF file.
**Dataset:** `wine.arff` – 178 instances, attributes alcohol, malic_acid, ash, flavanoids, color_intensity, proline (+ class, ignored).
**Procedure:** Weka Explorer → Open `wine.arff` → Cluster → SimpleKMeans (numClusters = 3, seed = 10) → Classes to clusters evaluation.
**Output (Python re-creation):** 7 iterations, within-cluster SSE 19.49; clusters 50 / 70 / 58 (28 % / 39 % / 33 %); 7 instances (3.93 %) incorrectly clustered.
**Result:** Three well-separated clusters. Image: `09_Simple_KMeans_Wine_ARFF_OUTPUT.png`

---
### Note on the images
`output.png/*.png` are rendered from real computations on these datasets (Python with the Orange 3.40 library, scikit-learn, mlxtend), so all numbers are genuine. They are **not** screenshots of the Orange/Weka windows. To get native screenshots, open the `.ows` files in Orange, point the File widget at the matching dataset in `datasets/`, and capture the widgets. Weka's numbers in Exp. 9 may differ slightly from the Python re-creation.
