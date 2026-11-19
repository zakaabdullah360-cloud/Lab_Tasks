# Comparative Implementation of Machine Learning Regression Algorithms
## House Price Prediction Experimental Results Report

**Name:** Muhammad Abdullah Zaka  
**Roll No:** FA23-BAI-017  
**Date:** 19 November 2026  

---

## 1. Dataset

* **Source:** House Sales Prediction dataset on Kaggle (`shree1992/housedata`), loaded in Google Colab using `kagglehub`. The CSV file used contains 4,600 houses and 18 columns.
* **Target variable:** `price` (converted to units of \$100,000, e.g., $12.25 = \$1,225,000$).
* **Input features:** `sqft_living` (in thousands of sq ft), `bedrooms`, `bathrooms`, `floors`, `condition`.
* **Dataset size used:** To keep the dataset small and simple, 100 houses were randomly sampled (`random_state = 42`) after removing missing values and invalid rows with `price = 0`.
* **Missing values after cleaning:** 0.

| Variable | Min | 25% | Median | 75% | Max |
| :--- | :---: | :---: | :---: | :---: | :---: |
| `sqft_living` ($\times 1000$) | 0.74 | 1.64 | 2.18 | 2.71 | 5.42 |
| `bedrooms` | 2 | 3 | 3 | 4 | 7 |
| `bathrooms` | 1.00 | 1.75 | 2.50 | 2.50 | 5.75 |
| `floors` | 1 | 1 | 1.5 | 2 | 3 |
| `condition` | 2 | 3 | 3 | 4 | 5 |
| `price` (\$100,000) | 1.83 | 3.38 | 4.85 | 6.77 | 16.90 |

*Table 1: Summary of the 100-house sample ($\text{mean price} = 5.36$, $\text{std} = 2.82$)*

---

## 2. Methodology

* **Train/test split:** 80:20 using `train_test_split` (`random_state = 42`), giving 80 training and 20 testing houses. The same split was used for all six models.
* **Feature scaling:** `StandardScaler` ($\text{mean} = 0$, $\text{standard deviation} = 1$) was applied to Linear Regression, KNN, and Neural Network because they depend on distances or gradient-based learning. It was fitted on the training data only to avoid data leakage. Decision Tree, Random Forest, and XGBoost use unscaled data because trees only compare thresholds.
* **Evaluation metrics:** MAE, MSE, RMSE, and $R^2$ Score on the 20 test houses.

| Model | Settings Used |
| :--- | :--- |
| **Linear Regression** | Default (ordinary least squares), scaled features |
| **Decision Tree** | `max_depth = 4`, `random_state = 42` |
| **Random Forest** | `n_estimators = 100`, `random_state = 42` |
| **Neural Network** | One hidden layer with 16 neurons, `max_iter = 5000`, scaled features |
| **XGBoost** | `n_estimators = 100`, `learning_rate = 0.1`, `max_depth = 3` |
| **KNN** | `n_neighbors = 5`, scaled features |

---

## 3. Experimental Results

### 3.1 Comparative Performance Table

| Model | MAE | MSE | RMSE | $R^2$ Score |
| :--- | :---: | :---: | :---: | :---: |
| **Linear Regression** | **1.491** | **3.423** | **1.850** | **0.679** |
| **Neural Network** | 1.522 | 3.608 | 1.899 | 0.662 |
| **Decision Tree** | 1.732 | 4.934 | 2.221 | 0.538 |
| **Random Forest** | 1.721 | 5.404 | 2.325 | 0.494 |
| **KNN** | 1.961 | 5.637 | 2.374 | 0.472 |
| **XGBoost** | 1.741 | 6.184 | 2.487 | 0.421 |

*Table 4: Test-set performance of the six models (error values are in units of \$100,000)*

### 3.2 Visual Comparison

![MAE, RMSE and R² of the six models](figure1.png)  
*Figure 1: MAE, RMSE and $R^2$ of the six models (Note: Replace `figure1.png` with the actual image path)*

### 3.3 Actual Versus Predicted Values

> *[Insert here the six actual-versus-predicted scatter plots produced in Google Colab (Cell 5 output). In each plot, the red dashed line shows perfect prediction; points closer to the line indicate more accurate predictions.]*

![Actual vs predicted prices for each model](figure2.png)  
*Figure 2: Actual vs predicted prices for each model (paste Colab image)*

---

## 4. Discussion of Results

* **Best model – Linear Regression:** It achieved the lowest MAE ($1.491$), MSE ($3.423$), and RMSE ($1.850$), as well as the highest $R^2$ ($0.679$). Its typical error is about \$149,000 (MAE) and it explains about $68\%$ of the variation in house prices.
* **Neural Network ($R^2 = 0.662$):** Was a close second. With scaled inputs, it can learn an almost linear relationship, so it performed nearly as well as Linear Regression.
* **Decision Tree, Random Forest, and KNN ($R^2$ between $0.47$ and $0.54$):** Performed moderately. Tree-based models predict group averages and cannot continue a trend, so a few very expensive houses (highest price $16.9$) reduced their accuracy.
* **XGBoost ($R^2 = 0.421$):** Had the lowest $R^2$ and the highest MSE. Boosting fits the training errors step by step and can overfit a very small training set of 80 houses; it also usually needs parameter tuning.
* **Reasons simple models performed better:** 
  1. The dataset is very small (80 training houses).
  2. House price rises roughly linearly with living area.
  3. Complex models need more data to show their advantage.

---

## 5. Strengths and Limitations of Each Algorithm

| Algorithm | Strengths | Limitations |
| :--- | :--- | :--- |
| **Linear Regression** | Fast, simple, and easy to interpret; works well when the relationship is roughly linear | Cannot capture non-linear relationships; sensitive to outliers |
| **Decision Tree** | Easy to understand and visualise; no scaling needed | Overfits easily; unstable; cannot extrapolate beyond training range |
| **Random Forest** | More accurate and stable than one tree; reduces overfitting; shows feature importance | Slower; harder to interpret; needs more data to excel |
| **Neural Network** | Can learn complex non-linear patterns | Needs scaling and lots of data; black box; many settings to choose |
| **XGBoost** | Very powerful on large tabular data; corrects errors step by step | Can overfit small data; needs careful tuning |
| **KNN** | Very simple; no training phase | Slow for large data; sensitive to scaling and the choice of $k$ |

*Table 5: Strengths and limitations*