# Data Dictionary

## Cost & Schedule Dataset (`Cost_Sch-data-v2.xls`)

The primary dataset containing historical oil and gas project attributes and their actual cost/schedule outcomes. Used for training and evaluating prediction models.

### Excel Sheets

| Sheet Name | Purpose |
|------------|---------|
| `Cost3` | Main training data — project attributes with cost and schedule overrun values |
| `Cost_T1` | Test set 1 — projects for prediction (reduced feature set: 13 and 7 features) |
| `Cost_T2` | Test set 2 — projects for prediction (full 39-feature set) |

### Feature Definitions

#### Raw Input Features (before encoding)

| # | Feature | Type | Description |
|---|---------|------|-------------|
| 1 | **Unit Name** | Text | Project/unit identifier (dropped before training) |
| 2 | **Region** | Categorical | Geographic region of the project (e.g., Asia-Pacific, West Africa, North Sea) |
| 3 | **Local Content** | Numerical | Local content requirement percentage or rating |
| 4 | **Lease/Own** | Categorical | Whether the asset is leased or owned |
| 5 | **Planned_Cost** | Numerical | Original planned/budgeted cost |
| 6 | **Hull Type** | Categorical | Type of hull (FPSO-specific: new build, conversion, etc.) |
| 7 | **Technology Novelty** | Numerical | Rating of how novel the technology is (higher = more novel) |
| 8 | **Type Unit** | Categorical | Type of production unit (FPSO, platform, subsea, etc.) |
| 9 | **Water_Depth** | Numerical | Water depth at project site (meters) |
| 10 | **Lessons Learned** | Categorical/Numerical | Whether lessons learned from prior projects were incorporated |
| 11 | **Oil/Gas** | Categorical | Whether the project is primarily oil or gas |
| 12 | **FEED Detail** | Categorical/Numerical | Level of detail in the FEED (Front-End Engineering Design) phase |

#### Target Variables

| Feature | Type | Description |
|---------|------|-------------|
| **Cost_Overrun** | Numerical (ratio) | Actual cost / planned cost ratio. Values > 1.0 indicate overrun |
| **Schedule_Overrun** | Numerical (ratio) | Actual duration / planned duration ratio. Values > 1.0 indicate overrun |

#### After One-Hot Encoding

The categorical features are expanded via `pd.get_dummies()`, producing **39 total features** for the full model. Feature reduction iterations use:

| Model Group | Feature Count | Selected Features |
|-------------|--------------|-------------------|
| model2, model3 | 39 | All one-hot encoded features |
| model4, model5 | 13 | Region, Local Content, Lease/Own, Planned_Cost, Hull Type, Technology Novelty, Type Unit, Water Depth, Lessons Learned, Oil/Gas, FEED Detail (column indices: 1,2,4,9,10,11,12,13,14,15,16,17,18) |
| model6, model7 | 7 | Region, Local Content, Technology Novelty, Type Unit, Lessons Learned, FEED Detail (column indices: 1,2,12,13,14,16,18) |

### Statistical Profile (Schedule Overrun)

Key statistics are computed in the notebook for outlier detection:
- Mean, median, min, max, standard deviation
- 2-sigma, 3-sigma, and 3.9-sigma thresholds for outlier filtering

## Performance Spreadsheets

### `Gereral_perf.xlsx` / `Gereral_perf2.xlsx`

General performance metric spreadsheets. These are supplementary data files used for cross-referencing project performance data. The two files represent different versions or subsets of the same performance dataset.

## XML Project Schedule Data

### MS Project XML Files (e.g., `FEED-Sch_v4.xml`)

Microsoft Project XML files following the `http://schemas.microsoft.com/project` namespace. Not included in the repository (loaded at runtime).

#### Key XML Elements Parsed

| Element | Description |
|---------|-------------|
| `Task` | Individual project task/activity |
| `Task/UID` | Unique task identifier (used as graph node ID) |
| `Task/Name` | Task name/description |
| `Task/Duration` | Task duration (ISO 8601 format, e.g., `PT480H0M0S`) |
| `Task/Start` | Planned start date |
| `Task/Finish` | Planned finish date |
| `Task/PredecessorLink` | Link to predecessor task(s) — used to build graph edges |
| `Task/PredecessorLink/PredecessorUID` | UID of the predecessor task |
| `OutlineCode` | Work breakdown structure (WBS) categories |

#### Graph Data Structures Built from XML

| Structure | Type | Description |
|-----------|------|-------------|
| `G_dense` | 2D array | Adjacency matrix (1 = dependency exists) |
| `G_names` | 2D array | Task names indexed by node ID |
| `G_dur` | 2D array | Task durations indexed by node ID |
| `G_std` | 2D array | Start dates indexed by node ID |
| `G_edd` | 2D array | End dates indexed by node ID |
| `Net1` | NetworkX DiGraph | Directed graph with weighted edges (weight = predecessor duration) |

#### Computed Graph Metrics (stored in `Proj1_PD` DataFrame)

| Column | Description |
|--------|-------------|
| `Number` | Task/node ID |
| `Name` | Task name |
| `Durations` | Task duration |
| `Start_Date` | Planned start date |
| `Finish_Date` | Planned finish date |
| `Predecessors` | List of predecessor node IDs |
| `Clustering_Coefficient` | Local clustering coefficient |
| `Degree_Centrality` | Degree centrality score |
| `closeness_centrality` | Closeness centrality score |
| `eigenvector_centrality` | Eigenvector centrality score |

## Output Files

| File | Source Notebook | Description |
|------|----------------|-------------|
| `Cost_Pred3.xlsx` | Predict_Cost3 | Cost overrun predictions from model2 and model3 with actuals |
| `Sch_Pred3.xlsx` | Predict_Cost3 | Schedule overrun predictions from all 6 models with actuals |
| `predict_cost.xlsx` | Predict_Cost3 | Raw model2 predictions (intermediate output) |
| `Degree_Centrality.txt` | XML_Proj1 | JSON export of sorted degree centrality values |
