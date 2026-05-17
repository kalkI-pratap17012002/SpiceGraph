# 🌶️ SpiceGraph

> **Uncovering the Network Architecture of Indian Regional Cuisines**

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python&logoColor=white)](https://www.python.org/)
[![NetworkX](https://img.shields.io/badge/NetworkX-3.x-orange)](https://networkx.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![Dataset](https://img.shields.io/badge/Dataset-CulinaryDB-green)](https://cosylab.iiitd.edu.in/culinarydb/)
[![Course](https://img.shields.io/badge/Course-Network%20Science%20%7C%20IIIT%20Delhi-purple)](https://www.iiitd.ac.in/)

---

## Overview

**SpiceGraph** models Indian regional cuisines as weighted graphs — spices are nodes, and recipe co-occurrence of two spices creates an edge between them. By applying five distinct network science lenses to these graphs, the project reveals deep structural patterns in how Indian food culture is organized.

The dataset is **CulinaryDB**, curated at IIIT Delhi by Prof. Ganesh Bagler, comprising thousands of recipes annotated with regional origin and ingredient-level detail.

---

## Why This Matters

Most food-science analyses treat recipes as flat ingredient lists. SpiceGraph treats them as **relational structures** — and asks what the *network topology* of Indian spice usage can tell us that list-based analysis cannot.

The results are surprisingly rich: community detection discovers real spice blends (Garam Masala, Panch Phoron, South Indian Tadka) *without being told they exist*. Robustness simulations reveal which regional cuisines are structurally fragile vs. resilient. Bridge spice detection explains, computationally, why certain spices like cumin and mustard seeds became the backbone of culinary fusion across traditions.

---

## Deliverables

| ID | Analysis | Core Question |
|---|---|---|
| **D1** | Power-law degree distribution test | Are Indian spice networks scale-free? |
| **D2** | Louvain community detection | Can algorithms discover real masala archetypes? |
| **D3** | Centrality vs replaceability scatter | Which spices are truly irreplaceable? |
| **D4** | Bridge spice detection (Pan-India graph) | What connects regional cuisines structurally? |
| **D5** | Targeted vs random removal robustness | How fragile are regional flavor networks? |

---

## Repository Structure

```
SpiceGraph/
├── SpiceGraph.ipynb          # Main analysis notebook (all 5 deliverables)
├── SpiceGraph.pptx           # Project presentation
├── Report.pdf                # Final project report
├── README.md                 # This file
├── CulinaryDB/               # Dataset
│   ├── 01_Recipe_Details.csv
│   ├── 02_Ingredients.csv
│   ├── 03_Compound_Ingredients.csv
│   └── 04_Recipe-Ingredients_Aliases.csv
└── Results/                  # Output plots (auto-generated)
```

---

## Setup & Usage

### 1. Clone the repo

```bash
git clone https://github.com/kalkI-pratap17012002/SpiceGraph.git
cd SpiceGraph
```

### 2. Install dependencies

```bash
pip install pandas numpy networkx matplotlib seaborn scipy
```

All libraries are standard scientific Python. No specialized packages required.

### 3. Run the notebook

```bash
jupyter notebook SpiceGraph.ipynb
```

Run all cells top to bottom. Output plots are saved as 300 DPI PNGs in `Results/`.

---

## Methodology

### Graph Construction

For each Indian regional cuisine (North Indian, South Indian, Bengali, Gujarati, Rajasthani, Maharashtrian), a weighted undirected graph is built:

- **Nodes** — spices that appear in at least one recipe of that region
- **Edges** — co-occurrence in at least 2 recipes (min-weight threshold to filter noise)
- **Edge weight** w(sᵢ, sⱼ) = number of recipes containing both sᵢ and sⱼ
- **Node attribute** — recipe frequency (how many recipes feature that spice)

A separate **Pan-India combined graph** merges all regions for D4.

---

### D1 — Scale-Free Network Test

Fits each regional degree distribution to a **power-law** P(k) ~ k^(-γ) and an **exponential** using log-linear regression. Compares R² goodness-of-fit.

- If power-law wins (R² > 0.75): the network is scale-free — a handful of "superstar" spices dominate, consistent with preferential attachment as recipes evolved culturally.
- If exponential wins: spice usage is more egalitarian, suggesting deliberate cultural curation rather than organic network growth.

Expected exponent range for scale-free networks: γ ∈ [2, 3].

---

### D2 — Masala Archetype Discovery

Runs the **Louvain community detection** algorithm on each regional graph (weighted, resolution=1.0). Communities are then matched to a dictionary of known real spice blends using **Jaccard similarity**:

J(A, B) = |A ∩ B| / |A ∪ B|

Known blends checked: Garam Masala, South Indian Tadka, Sambhar Masala, Bengali Panch Phoron, Biryani Blend, Basic North Indian. The algorithm discovers these blends *purely from co-occurrence structure* — no blend information is used during community detection.

---

### D3 — Spice Removal Paradox

Tests the hypothesis: **high centrality ≠ high irreplaceability**.

1. Computes **betweenness centrality** for every spice in each regional graph.
2. Computes a **replaceability score** — a spice is more replaceable if its neighbors share a high fraction of its flavor molecules (using FlavorDB molecule counts per ingredient, with a published-data fallback for common spices).
3. Plots each spice on a 2D scatter: x = betweenness centrality, y = replaceability.

The top-left quadrant (high centrality, low replaceability) identifies the truly irreplaceable spices of each cuisine.

---

### D4 — Bridge Spice Detection

On the Pan-India graph:

1. Runs Louvain community detection — communities roughly correspond to regional flavor clusters.
2. For each spice, computes a **bridge score** = betweenness centrality × (fraction of neighbors in different communities).
3. Top bridge spices are those that structurally span multiple regional communities.

Expected result: cumin bridges North and South Indian traditions; mustard seeds bridge Bengali and South Indian; cardamom bridges Mughal North Indian and Kerala coastal cuisine.

---

### D5 — Robustness Simulation

Simulates two removal strategies on each regional graph:

- **Targeted attack** — adaptive, re-identifies the highest-degree node after each removal (correct implementation; static pre-ordering underestimates damage).
- **Random removal** — averaged over 15 independent trials.

After each removal step, measures **LCC fraction** (size of largest connected component / original graph size). Plots robustness curves for all regions on a single comparison figure.

Classic prediction for scale-free networks: robust to random failures, fragile under targeted attack. Whether Indian spice networks follow this pattern — or break it — is the finding.

---

## Output Plots

| File | Content |
|---|---|
| `D1_PowerLaw_<Region>.png` | Log-log degree distribution + power-law vs exponential fit |
| `D2_Communities_<Region>.png` | Network colored by Louvain community |
| `D2_Heatmap_<Region>.png` | Community × known blend Jaccard similarity heatmap |
| `D3_Centrality_Replaceability_<Region>.png` | Spice removal paradox scatter |
| `D4_BridgeNetwork_PanIndia.png` | Pan-India graph with bridge spices highlighted |
| `D4_BridgeScores.png` | Ranked bar chart of top bridge spices |
| `D5_Robustness_<Region>.png` | Per-region robustness curves |
| `D5_RobustnessComparison.png` | All regions, targeted vs random, side-by-side |

---

## Dataset

**CulinaryDB** — Computational Gastronomy Lab, IIIT Delhi  
PI: Prof. Ganesh Bagler  
URL: https://cosylab.iiitd.edu.in/culinarydb/

The database contains recipes from 84 regional cuisines globally, with structured ingredient annotations linked to FlavorDB flavor molecule data. This project uses the Indian regional subset.

**FlavorDB** molecule counts (for D3) are sourced from:  
> Garg, N. et al. (2018). *FlavorDB: a database of flavor molecules*. Nucleic Acids Research.

---

## Tech Stack

| Library | Purpose |
|---|---|
| `pandas` | Data loading, filtering, and joining the 4 CulinaryDB CSVs |
| `networkx` | Graph construction, Louvain community detection, centrality measures |
| `matplotlib` / `seaborn` | All visualizations (saved at 300 DPI) |
| `scipy` | Log-linear regression for power-law fitting |
| `itertools` | Efficient pairwise spice combination generation |

Python 3.9+ · No GPU required · Runs on a standard laptop in under 5 minutes.

---

## License

This project is for academic and research purposes. CulinaryDB is used under its non-commercial research license from IIIT Delhi.

---

*Network Science course project · IIIT Delhi*
