# Model Documentation

## Overview

The cost and schedule prediction system uses multiple deep neural network (DNN) architectures implemented with Keras/TensorFlow. Models are trained in `Predict_Cost3.ipynb` and vary by feature count and network depth, allowing comparison of prediction performance across different configurations.

## Model Inventory

### Full Feature Models (39 features)

#### Model 2

| Parameter | Value |
|-----------|-------|
| **Input features** | 39 (all one-hot encoded) |
| **Architecture** | Dense(39, relu) → Dense(78, relu) → Dense(39, relu) → Dense(7, relu) → Dense(1, sigmoid) |
| **Loss** | MSE |
| **Optimizer** | Adam |
| **Epochs** | 150 |
| **Batch size** | 9 |
| **Validation** | 10-fold cross-validation (KFold) |
| **Target** | Cost overrun / Schedule overrun |

#### Model 3

| Parameter | Value |
|-----------|-------|
| **Input features** | 39 (all one-hot encoded) |
| **Architecture** | Dense(39, sigmoid) → Dense(78, sigmoid) → Dense(39, sigmoid) → Dense(7, sigmoid) → Dense(1, sigmoid) |
| **Loss** | MSE |
| **Optimizer** | SGD (lr=0.1, momentum=0.8, decay=lr/epochs, Nesterov=off) |
| **Epochs** | 400 |
| **Batch size** | 9 |
| **Validation** | 10-fold cross-validation |

### Reduced Feature Models (13 features)

Features selected via Pearson correlation analysis: Region, Local Content, Lease/Own, Planned Cost, Hull Type, Technology Novelty, Type Unit, Water Depth, Lessons Learned, Oil/Gas, FEED Detail.

#### Model 4

| Parameter | Value |
|-----------|-------|
| **Input features** | 13 |
| **Architecture** | Dense(13, sigmoid) → Dense(26, sigmoid) → Dense(8, sigmoid) → Dense(1, sigmoid) |
| **Loss** | MSE |
| **Optimizer** | Adam |
| **Epochs** | 150 |
| **Batch size** | 9 |

#### Model 5

| Parameter | Value |
|-----------|-------|
| **Input features** | 13 |
| **Architecture** | Dense(26, sigmoid) → Dense(13, sigmoid) → Dense(1, sigmoid) |
| **Loss** | MSE |
| **Optimizer** | SGD (lr=0.1, momentum=0.8, decay=lr/epochs) |
| **Epochs** | 400 |
| **Batch size** | 9 |

### Minimal Feature Models (7 features)

Features further reduced via Pearson correlation: Region, Local Content, Technology Novelty, Type Unit, Lessons Learned, FEED Detail.

#### Model 6

| Parameter | Value |
|-----------|-------|
| **Input features** | 7 |
| **Architecture** | Dense(12, relu) → Dense(8, relu) → Dense(1, sigmoid) |
| **Loss** | MSE |
| **Optimizer** | Adam |
| **Epochs** | 150 |
| **Batch size** | 9 |

#### Model 7

| Parameter | Value |
|-----------|-------|
| **Input features** | 7 |
| **Architecture** | Dense(14, sigmoid) → Dense(7, sigmoid) → Dense(1, sigmoid) |
| **Loss** | MSE |
| **Optimizer** | SGD (lr=0.1, momentum=0.8, decay=lr/epochs) |
| **Epochs** | 400 |
| **Batch size** | 9 |

## Training Pipeline

### Data Preprocessing

1. **Load data** from `Cost_Sch-data-v2.xlsx` (sheet `Cost3`)
2. **Drop** non-predictive columns (`Unit Name`, then target columns)
3. **One-hot encode** categorical features using `pd.get_dummies()`
4. **Re-attach** target variable (`Cost_Overrun` or `Schedule_Overrun`)
5. **Convert** target to numeric, coercing errors
6. **Normalize** features using `MinMaxScaler` (scales to [0, 1])

### Data Split

| Set | Proportion | Purpose |
|-----|-----------|---------|
| Training | 70% | Model fitting |
| Validation | 15% | Hyperparameter tuning / early stopping |
| Test | 15% | Final evaluation |

Split performed using `train_test_split` with a fixed random seed of 7 for reproducibility.

### Cross-Validation

- **Method:** 10-fold KFold (not stratified, since target is continuous)
- **Seed:** 7 (fixed for reproducibility)
- **Metric:** Accuracy collected per fold in `cvscores` / `cvscores2`

### Feature Selection Strategy

Feature reduction is guided by **Pearson correlation analysis**:

1. Compute full correlation matrix (`df.corr(method='pearson')`)
2. Visualize with seaborn heatmap (diverging palette, 20x20 figure)
3. Remove features with low correlation to target or high multicollinearity
4. Three tiers: 39 features → 13 features → 7 features

### Evaluation Metrics

- **Loss:** Mean Squared Error (MSE)
- **Visual:** Training vs. validation loss curves plotted per model
- **Comparison:** Predictions from all 6 models exported side-by-side to Excel with actual values

## Prediction Pipeline

### For New Projects

1. Load test data from `Cost_Sch-data-v2.xlsx` (sheets `Cost_T1` or `Cost_T2`)
2. Drop non-feature columns (Unit Name, Cost_Overrun, Schedule_Overrun)
3. One-hot encode (for 39-feature models) or select column indices (for 13/7-feature models)
4. Normalize with `MinMaxScaler`
5. Call `model.predict()` for each trained model
6. Export predictions to Excel:
   - `Cost_Pred3.xlsx` — columns: model2 predictions, model3 predictions, actual values, project names
   - `Sch_Pred3.xlsx` — columns: all 6 model predictions, actual values, project names

### Output Format

**Cost_Pred3.xlsx:**

| Column | Source |
|--------|--------|
| `0` | model2 predictions (39 features, relu) |
| `18-2` | model3 predictions (39 features, sigmoid) |
| `actual` | Actual cost overrun from test data |
| `Name` | Project unit name |

**Sch_Pred3.xlsx:**

| Column | Source |
|--------|--------|
| `0` | model2 predictions (39 features) |
| `18-2` | model3 predictions (39 features) |
| `13-1` | model4 predictions (13 features) |
| `13-2` | model5 predictions (13 features) |
| `7-1` | model6 predictions (7 features) |
| `7-2` | model7 predictions (7 features) |
| `actual` | Actual schedule overrun |
| `Name` | Project unit name |

## Known Limitations

1. **Small dataset** — Training data is limited; model generalization may be poor
2. **No hyperparameter search** — All hyperparameters are manually set, not optimized via grid/random search
3. **Deprecated imports** — Uses `from keras.models import Sequential` instead of `from tensorflow.keras.models import Sequential`
4. **MinMaxScaler refit** — Scaler is re-fit on test data instead of using the training scaler transform, which can cause data leakage
5. **Accuracy metric for regression** — Some models use `metrics=['accuracy']` which is not meaningful for regression tasks; MSE or MAE would be more appropriate
6. **No model persistence** — Models are not saved to disk; must retrain on each notebook run
7. **No ensemble** — Models are compared side-by-side but not combined into an ensemble prediction
