# Product Overview

## Vision

Provide oil and gas project managers, estimators, and decision-makers with data-driven tools to predict project cost and schedule overruns before they occur, and to analyze project schedule structures for hidden risks and critical dependencies.

## Problem Statement

Oil and gas capital projects — particularly FPSO (Floating Production Storage and Offloading) conversions, platform construction, and subsea installations — are among the most complex engineering endeavors. Industry data shows these projects routinely exceed their budgets and timelines. Key challenges include:

- **Lack of early warning signals:** Overruns are often detected too late in the project lifecycle
- **Complex interdependencies:** Hundreds of tasks with intricate predecessor/successor relationships
- **Limited historical benchmarking:** Few standardized tools exist to compare project attributes against historical outcomes
- **Schedule opacity:** Critical path analysis alone does not reveal structural vulnerabilities in project networks

## Target Users

| User Role | Primary Use Case |
|-----------|-----------------|
| **Project Managers** | Assess overrun risk during FEED (Front-End Engineering Design) phase |
| **Cost Estimators** | Validate estimates against ML predictions based on similar historical projects |
| **Planning Engineers** | Identify schedule bottlenecks, bridges, and community structures in project networks |
| **Portfolio Managers** | Compare risk profiles across multiple capital projects |
| **Research Analysts** | Study correlations between project attributes and performance outcomes |

## Product Components

### Component 1: Cost & Schedule Overrun Predictor

**Purpose:** Predict the likelihood and magnitude of cost and schedule overruns based on project characteristics known at the FEED stage.

**Key Features:**
- Predicts cost overrun ratio and schedule overrun ratio
- Uses 11 categorical and numerical project attributes as inputs
- Iterative feature reduction (39 → 13 → 7 features) to identify most predictive factors
- Multiple model architectures for ensemble-style comparison
- Exports predictions to Excel for stakeholder review

**Input Attributes:**
- Region (geographic location)
- Local content requirements
- Lease vs. own arrangement
- Planned cost
- Hull type
- Technology novelty
- Unit type (FPSO, platform, etc.)
- Water depth
- Lessons learned incorporation
- Oil vs. gas classification
- FEED detail level

### Component 2: Project Schedule Network Analyzer

**Purpose:** Parse project schedules from MS Project XML format and apply graph-theoretic analysis to reveal structural properties, critical dependencies, and potential risk areas.

**Key Features:**
- XML parsing of Microsoft Project files
- Directed graph construction from task dependencies
- Adjacency matrix and sparse matrix representations
- Network centrality metrics (degree, closeness, eigenvector)
- Community detection (Girvan-Newman, greedy modularity, Kernighan-Lin)
- Bridge and clique identification
- Interactive Gantt chart visualization (Plotly)
- Optional Neo4j graph database storage for querying
- Blockchain-inspired genesis block creation for schedule versioning

**Graph Metrics Computed:**
- Degree centrality — identifies tasks with the most connections
- Closeness centrality — identifies tasks closest to all others (potential bottlenecks)
- Eigenvector centrality — identifies tasks connected to other important tasks
- Clustering coefficient — measures local interconnectedness
- Bridges — edges whose removal disconnects the graph (single points of failure)
- DAG longest path — critical path length
- Connectivity checks (strong, weak, Eulerian, acyclic)

## Data Flow

```
┌─────────────────────┐     ┌──────────────────────┐     ┌──────────────────┐
│   Excel Data Files  │────▶│  Predict_Cost3.ipynb  │────▶│  Cost_Pred3.xlsx │
│  Cost_Sch-data-v2   │     │  (ML Training &       │     │  Sch_Pred3.xlsx  │
│                     │     │   Prediction)          │     │                  │
└─────────────────────┘     └──────────────────────┘     └──────────────────┘

┌─────────────────────┐     ┌──────────────────────┐     ┌──────────────────┐
│  MS Project XML     │────▶│  XML_Proj1 notebook   │────▶│  Graph Metrics   │
│  (FEED-Sch_v4.xml)  │     │  (Graph Analysis)     │     │  Visualizations  │
│                     │     │                        │────▶│  Neo4j Database  │
└─────────────────────┘     └──────────────────────┘     └──────────────────┘
```

## Current Status

| Aspect | Status |
|--------|--------|
| Cost prediction models | Functional — 6 model variants trained |
| Schedule prediction | Functional — predictions exported to Excel |
| XML graph analysis | Functional — full graph metrics pipeline |
| Neo4j integration | Implemented — requires local Neo4j instance |
| Gantt chart visualization | Functional — Plotly interactive charts |
| Community detection | Implemented — multiple algorithms |
| Automated pipeline | Not yet — manual notebook execution |
| Web interface | Not yet — notebook-only |
| Test suite | Not yet — validation via notebook outputs |

## Future Roadmap Considerations

1. **Modularize code** — Extract reusable functions from notebooks into Python modules
2. **Add requirements.txt** — Pin dependency versions for reproducibility
3. **Build automated pipeline** — Script to run prediction end-to-end without notebook interaction
4. **Web dashboard** — Interactive interface for non-technical stakeholders
5. **Model registry** — Track model versions, hyperparameters, and performance metrics
6. **Expand dataset** — Incorporate more historical project data for improved predictions
7. **Update Keras imports** — Migrate from standalone `keras` to `tensorflow.keras`
