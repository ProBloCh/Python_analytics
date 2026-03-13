# Python Analytics - Oil & Gas Project Performance Prediction

A Python-based analytics toolkit for predicting cost and schedule overruns in oil and gas capital projects, combined with network graph analysis of project schedules.

## Problem Statement

Large-scale oil and gas capital projects (FPSO, platforms, subsea systems) frequently experience cost and schedule overruns. This project uses machine learning to predict overruns based on project characteristics, and network analysis to identify critical dependencies and bottlenecks in project schedules.

## Components

### 1. Cost & Schedule Prediction (`Predict_Cost3.ipynb`)

Trains deep neural networks to predict cost and schedule overruns using project attributes such as region, water depth, technology novelty, and local content requirements.

- **Input:** Project attribute data from `Cost_Sch-data-v2.xls`
- **Output:** Prediction spreadsheets (`Cost_Pred3.xlsx`, `Sch_Pred3.xlsx`)
- **Models:** 6 model variants (model2-model7) with 39, 13, and 7 feature configurations

### 2. Project Schedule Graph Analysis (`XML_Proj1-Copy2 3-Copy1.ipynb`)

Parses Microsoft Project XML files to build directed network graphs, then computes graph-theoretic metrics to identify critical paths, bottlenecks, and community structures.

- **Input:** MS Project XML files (e.g., `FEED-Sch_v4.xml`)
- **Output:** Graph metrics, Gantt charts, community detection results
- **Storage:** Optional Neo4j graph database integration

## Quick Start

### Prerequisites

```
Python 3.8+
Jupyter Notebook or JupyterLab
```

### Install Dependencies

```bash
pip install pandas numpy scipy matplotlib seaborn plotly
pip install tensorflow scikit-learn keras
pip install networkx py2neo neo4j neo4jupyter
pip install openpyxl xlrd
```

### Run

```bash
jupyter notebook Predict_Cost3.ipynb        # Cost/schedule prediction
jupyter notebook "XML_Proj1-Copy2 3-Copy1.ipynb"  # Graph analysis
```

## Documentation

| Document | Description |
|----------|-------------|
| [Product Overview](docs/product-overview.md) | Vision, use cases, and target users |
| [Data Dictionary](docs/data-dictionary.md) | Dataset schemas and field definitions |
| [Model Documentation](docs/model-documentation.md) | ML model architectures and evaluation |
| [User Guide](docs/user-guide.md) | Step-by-step usage instructions |
| [CLAUDE.md](CLAUDE.md) | AI assistant development guide |

## Project Structure

```
Python_analytics/
├── Predict_Cost3.ipynb            # ML prediction notebook
├── XML_Proj1-Copy2 3-Copy1.ipynb  # Graph analysis notebook
├── Cost_Sch-data-v2.xls           # Training/test dataset
├── Gereral_perf.xlsx              # Performance metrics data
├── Gereral_perf2.xlsx             # Performance metrics data (variant)
├── site.css                       # Notebook HTML export styling
├── style.css                      # Notebook HTML export styling
├── docs/                          # Product & technical documentation
│   ├── product-overview.md
│   ├── data-dictionary.md
│   ├── model-documentation.md
│   └── user-guide.md
├── CLAUDE.md                      # AI assistant guide
└── README.md                      # This file
```

## License

This project is for internal research and analysis purposes.
