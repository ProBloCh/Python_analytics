# CLAUDE.md - AI Assistant Guide for Python_analytics

## Project Overview

This is a **Python analytics and machine learning project** for predicting cost and schedule overruns in oil/gas engineering projects, plus a companion network graph analysis tool for project dependency analysis. The work is conducted primarily in Jupyter notebooks.

## Repository Structure

```
Python_analytics/
├── Predict_Cost3.ipynb            # ML cost/schedule prediction (Keras/TensorFlow)
├── XML_Proj1-Copy2 3-Copy1.ipynb  # XML parsing & network graph analysis (NetworkX/Neo4j)
├── Cost_Sch-data-v2.xls           # Main dataset: project cost/schedule attributes
├── Gereral_perf.xlsx              # Performance metrics spreadsheet
├── Gereral_perf2.xlsx             # Performance metrics spreadsheet (variant)
├── site.css                       # CSS stylesheet for notebook HTML export
├── style.css                      # CSS stylesheet for notebook HTML export
└── CLAUDE.md                      # This file
```

There is no formal Python package structure. All analysis lives in Jupyter notebooks.

## Key Components

### 1. Cost & Schedule Prediction (`Predict_Cost3.ipynb`)

- Predicts cost overrun and schedule overrun using deep neural networks
- Input features: region, local content requirements, water depth, technology novelty, project type, and more (11+ features)
- Architecture: Sequential Keras models with 3-5 hidden layers, sigmoid/ReLU activations, MSE loss
- Data split: 70% train / 15% validation / 15% test with 10-fold cross-validation (KFold)
- Normalization: MinMaxScaler on features
- Output: predictions exported to Excel (Cost_Pred3.xlsx, Sch_Pred3.xlsx)

### 2. XML/Network Graph Analysis (`XML_Proj1-Copy2 3-Copy1.ipynb`)

- Parses XML project files (Microsoft Project format)
- Builds network graphs of project milestones and dependencies
- Computes graph metrics: degree centrality, closeness centrality, eigenvector centrality, bridges, chains
- Uses NetworkX for graph computation and Neo4j (py2neo) for graph database storage
- 198 cells with extensive visualization

## Tech Stack & Dependencies

**Python version:** 3.8.5 (from notebook metadata)

**Core libraries:**
- **Data:** pandas, numpy, scipy
- **ML/DL:** tensorflow, keras, scikit-learn
- **Visualization:** matplotlib, seaborn, plotly.express
- **Graph:** networkx, py2neo, neo4jupyter
- **Preprocessing:** sklearn.preprocessing (MinMaxScaler, LabelEncoder, OneHotEncoder)
- **Utilities:** csv, re, hashlib, operator, datetime

> **Note:** There is no `requirements.txt` or `pyproject.toml`. Dependencies must be installed manually.

## Development Conventions

### Workflow
- All development happens in **Jupyter notebooks**
- Data is loaded from and exported to **Excel files** (.xls/.xlsx)
- No formal test suite, CI/CD pipeline, or build system exists
- Git history uses simple "Add files via upload" messages

### Code Patterns
- Inline data preprocessing (one-hot encoding, normalization) within notebooks
- Multiple model iterations (model2 through model7) defined sequentially with varying features/architectures
- Hyperparameters set as literal values (batch_size=9, epochs=150/400, learning_rate, momentum)
- Results evaluated with RMSE, MAE, and R-squared metrics

### Known Issues
- Keras imports use deprecated `from keras.*` style instead of `from tensorflow.keras.*`
- Commented-out code blocks remain in notebooks
- No docstrings or inline documentation

## Guidelines for AI Assistants

1. **Do not restructure** the notebook-based workflow unless explicitly asked - this is an exploratory data science project
2. **Preserve data files** - the Excel files are primary data sources; do not delete or overwrite them
3. **When modifying notebooks**, maintain existing cell structure and output where possible
4. **Use `tensorflow.keras`** imports (not standalone `keras`) for any new code
5. **Keep Excel I/O** as the data interchange format - this is intentional for stakeholder review
6. **Be cautious with large notebooks** - `XML_Proj1-Copy2 3-Copy1.ipynb` is ~11 MB with 198 cells
7. **No formal testing exists** - validate changes by running relevant notebook cells
8. **Git branch convention**: feature branches use `claude/` prefix
