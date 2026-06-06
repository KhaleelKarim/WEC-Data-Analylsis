# Wave Energy Converter Farm Surrogate Modeling

## Overview

This project develops machine learning surrogate models to predict the power output of Wave Energy Converter (WEC) farms using turbine layout information. The goal is to replace the computationally expensive simulations with fast predictive models that can estimate farm performance from the geometric layout features.

The project examines multiple machine learning approaches, explores feature engineering techniques, analyzes interaction efficiency (qW), and compares dimensionality reduction using PCA.

---

## Dataset

The analysis uses four WEC farm datasets from two locations:

* Sydney 49
* Sydney 100
* Perth 49
* Perth 100

Each dataset contains turbine coordinate information and power production measurements for different farm layouts.

---

## Methods

### Models Evaluated

* Dummy Regressor
* Linear Regression
* Gradient Boosting
* Random Forest
* Hist Gradient Boosting

### Feature Engineering

In addition to the raw turbine coordinates, several geometry-based features were created:

* centroid_x
* centroid_y
* x_std
* y_std
* x_range
* y_range
* mean_dist_center
* max_dist_center

These features summarize the overall farm geometry and turbine spacing.

### Additional Analysis

* Feature Importance
* Correlation Analysis
* 5-Fold Cross Validation
* Dataset Generalization Testing
* qW Interaction Efficiency Modeling
* Principal Component Analysis (PCA)

---

## Results

### Validation Performance

| Model                                      | Validation MAE | Validation RMSE | Validation R² |
| ------------------------------------------ | -------------- | --------------- | ------------- |
| Dummy Regressor                            | 59134          | 72098           | -0.001        |
| Linear Regression                          | 16396          | 23534           | 0.893         |
| Gradient Boosting                          | 8003           | 12158           | 0.972         |
| Hist Gradient Boosting (Raw Coordinates)   | 3488           | 7236            | 0.990         |
| Random Forest (Raw Coordinates)            | 3669           | 10285           | 0.980         |
| Random Forest + Geometry Features          | 2731           | 7277            | 0.990         |
| Hist Gradient Boosting + Geometry Features | 2777           | 5695            | 0.994         |

### Final Model Performance

**Hist Gradient Boosting + Geometry Features**

| Metric | Test Value |
| ------ | ---------- |
| MAE    | 2695.07    |
| RMSE   | 5436.69    |
| R²     | 0.99427    |

---

## Key Findings

* Tree-based ensemble models significantly outperformed the linear models.
* Geometry-based features improved predictive performance for both Random Forest and Hist Gradient Boosting.
* The most important predictors were:

  * `mean_dist_center`
  * `x_std`
  * `y_std`
  * `x_range`
* Overall farm geometry was more informative than individual turbine coordinates.
* The same geometry features were also highly predictive of qW interaction efficiency.
* PCA reduced the feature space from 106 variables to 13 principal components while retaining 95.8% of the variance, but predictive performance decreased substantially.

---

## Conclusion

The results from the project show how that engineered geometry features combined with Hist Gradient Boosting provide an accurate and reliable surrogate modeling approach for predicting WEC farm performance. The project also highlights the importance of turbine spacing and the overall farm layout in determining both power production and interaction efficiency.

---

## Technologies Used

* Python
* Pandas
* NumPy
* Scikit-Learn
* Matplotlib
* Seaborn
* Jupyter Notebook
