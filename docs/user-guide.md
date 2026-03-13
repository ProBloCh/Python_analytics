# User Guide

## Prerequisites

### Software Requirements

| Software | Version | Purpose |
|----------|---------|---------|
| Python | 3.8+ | Runtime |
| Jupyter Notebook | Any recent | Interactive execution environment |
| Neo4j | 3.x or 4.x | Graph database (optional, for XML analysis only) |

### Python Dependencies

```bash
# Core data science
pip install pandas numpy scipy matplotlib seaborn plotly

# Machine learning
pip install tensorflow scikit-learn

# Graph analysis
pip install networkx py2neo neo4j neo4jupyter

# Excel file support
pip install openpyxl xlrd
```

## Part 1: Cost & Schedule Prediction

### Step 1: Prepare Your Data

The training data must be in Excel format matching the schema in `Cost_Sch-data-v2.xls`:

- **Sheet `Cost3`** — Training data with all features and target variables
- **Sheet `Cost_T1`** — Test projects (for 13-feature and 7-feature models)
- **Sheet `Cost_T2`** — Test projects (for full 39-feature models)

Required columns: Region, Local Content, Lease/Own, Planned_Cost, Hull Type, Technology Novelty, Type Unit, Water_Depth, Lessons Learned, Oil/Gas, FEED Detail, Cost_Overrun, Schedule_Overrun, Unit Name.

### Step 2: Open the Notebook

```bash
jupyter notebook Predict_Cost3.ipynb
```

### Step 3: Run the Analysis Pipeline

Execute cells in order. The notebook is organized into these sections:

| Section | Cells | What It Does |
|---------|-------|-------------|
| **Imports & Data Loading** | 1-2 | Load libraries, read Excel data |
| **Exploratory Analysis** | 3-6 | Statistical summaries, outlier detection |
| **Preprocessing** | 7-18 | Drop columns, one-hot encode, correlation heatmaps |
| **39-Feature Models** | 19-42 | Train model2 (relu/Adam) and model3 (sigmoid/SGD) |
| **Predictions (39 features)** | 43-67 | Load test data, predict, export to Cost_Pred3.xlsx |
| **13-Feature Models** | 68-85 | Feature reduction, train model4 and model5 |
| **Predictions (13 features)** | 86-91 | Predict on test data |
| **7-Feature Models** | 92-101 | Further reduction, train model6 and model7 |
| **Predictions (7 features)** | 102-109 | Predict, compile all results to Sch_Pred3.xlsx |

### Step 4: Review Results

Output files are written to the working directory:

- **`Cost_Pred3.xlsx`** — Cost overrun predictions vs. actuals
- **`Sch_Pred3.xlsx`** — Schedule overrun predictions from all 6 models vs. actuals

Open these in Excel to compare model predictions against actual outcomes.

### Step 5: Interpret Loss Curves

Each model section includes a loss plot (training vs. validation). Look for:

- **Convergence** — Both curves should decrease and flatten
- **Overfitting** — Training loss decreasing while validation loss increases
- **Underfitting** — Both curves remain high and flat

## Part 2: Project Schedule Graph Analysis

### Step 1: Prepare XML File

Export your project schedule from Microsoft Project as XML:
- File → Save As → XML format
- Place the XML file in the working directory

Update the filename in the notebook (cell 5):
```python
tree = ET.parse('YOUR_PROJECT_FILE.xml')
```

### Step 2: Configure Neo4j (Optional)

If using Neo4j for graph storage:

1. Install and start Neo4j locally
2. Ensure it's running on `bolt://localhost:7687`
3. Default credentials: username `neo4j`, password `password`

The notebook will work without Neo4j — NetworkX analysis runs independently.

### Step 3: Open the Notebook

```bash
jupyter notebook "XML_Proj1-Copy2 3-Copy1.ipynb"
```

### Step 4: Run the Analysis Pipeline

| Section | What It Does |
|---------|-------------|
| **Imports & XML Parsing** | Load libraries, parse XML, extract tasks |
| **Graph Construction** | Build adjacency matrix, create NetworkX DiGraph |
| **Node Repair** | Connect hanging nodes (no predecessors/successors) to start/end |
| **DataFrame Assembly** | Build `Proj1_PD` with task attributes and predecessors |
| **Gantt Chart** | Interactive Plotly timeline visualization |
| **Centrality Metrics** | Degree, closeness, eigenvector centrality |
| **Graph Properties** | Connectivity, DAG validation, longest path, transitivity |
| **Community Detection** | Girvan-Newman, greedy modularity, k-clique, dominating sets |
| **Bridge Analysis** | Identify critical edges (single points of failure) |

### Step 5: Interpret Graph Metrics

| Metric | High Value Means | Risk Implication |
|--------|-----------------|------------------|
| **Degree centrality** | Task has many connections | Disruption affects many downstream tasks |
| **Closeness centrality** | Task is reachable from most others quickly | Delay propagates broadly through the network |
| **Eigenvector centrality** | Task is connected to other important tasks | Failure cascades through high-impact paths |
| **Clustering coefficient** | Task neighbors are also connected to each other | Local cluster — delays may be contained |
| **Bridge** | Removing this edge disconnects the graph | Single point of failure — requires risk mitigation |

### Step 6: Review Outputs

- **Gantt chart** — Interactive timeline (Plotly) color-coded by duration
- **Degree_Centrality.txt** — JSON file with sorted centrality values
- **`Proj1_PD` DataFrame** — Master table with all computed metrics per task
- **Neo4j database** — Query graph relationships interactively (if configured)

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `ModuleNotFoundError: No module named 'keras'` | Install TensorFlow: `pip install tensorflow` |
| `xlrd.biffh.XLRDError: Excel xlsx file; not supported` | Install openpyxl: `pip install openpyxl` |
| `AttributeError: module 'tkinter' has no attribute 'filedialog'` | This cell can be skipped — it's an optional file dialog |
| Neo4j connection refused | Start Neo4j service: `neo4j start`, or skip Neo4j cells |
| `DeprecationWarning` from Keras | Non-blocking; update imports to `from tensorflow.keras...` to suppress |
| Memory issues with large XML files | Reduce graph size by filtering tasks before building the adjacency matrix |
