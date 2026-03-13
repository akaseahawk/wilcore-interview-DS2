# Wilcore Technologies — Diamond Pricing EDA

Exploratory data analysis of the **P2-Mispriced-Diamonds** dataset, completed as part of the
[Remote Data Scientist 2 interview challenge](https://workforcenow.adp.com/mascsr/default/mdf/recruitment/recruitment.html?cid=f7dc51cf-bcd4-4d90-8703-f151ed980046&ccId=9201427462023_3&lang=en_US&jobId=559006&jwId=9201427462023_1).

---

## Project Structure

```
.
├── P2-Mispriced-Diamonds.csv   # Raw dataset (53,940 rows)
├── diamond_analysis.ipynb      # Main analysis notebook (fully executed)
├── diamond_analysis.html       # Static HTML export of the executed notebook
├── Request.md                  # Original challenge requirements
└── README.md                   # This file
```

---

## Setup & Running Locally

### Prerequisites

- Python 3.9+
- pip

### Installation

```bash
# Clone the repository
git clone https://github.com/akaseahawk/wilcore-interview-DS2.git
cd wilcore-interview-DS2

# Install dependencies
pip install pandas matplotlib seaborn jupyter nbconvert
```

### Run the Notebook

```bash
jupyter notebook diamond_analysis.ipynb
```

Or to re-execute it non-interactively:

```bash
jupyter nbconvert --to notebook --execute --inplace diamond_analysis.ipynb
```

### View as HTML (no Jupyter required)

Open `diamond_analysis.html` directly in any web browser to view the fully executed notebook
with all charts and outputs rendered.

---

## Assumptions

| Assumption | Rationale |
|------------ |-----------|
| **Duplicate rows are true duplicates** | All 20,584 duplicate rows were identical across all 3 columns (carat + clarity + price). These were removed as data entry artifacts, not meaningful repeated observations. |
| **IQR outlier removal applied per clarity group** | Different clarity grades have inherently different carat/price distributions. Applying a global IQR threshold would incorrectly flag valid high-carat SI-grade stones or miss noise in IF/VVS1 grades. Per-group removal is statistically sound. |
| **"Mispriced" refers to overlap zones between clarity tiers** | The dataset name suggests pricing anomalies where diamonds of adjacent clarity grades command the same price for equivalent carat weights — these overlapping zones are the primary analytical target. |
| **No ordinal encoding of clarity** | The challenge asks for categorical analysis; clarity is treated as a nominal category throughout rather than an ordered scale (I1 < SI2 < SI1 < VS2 < VS1 < VVS2 < VVS1 < IF), though the ordering is noted in markdown commentary. |

---

## Notebook Sections

| Section | Description |
|---------|-------------|
| **0. Imports** | Loads pandas, numpy, matplotlib, seaborn; sets global plot style |
| **1. Ingestion** | Reads `P2-Mispriced-Diamonds.csv`; previews first 10 rows |
| **2. Discovery** | Reports shape (53,940 × 3), dtypes, missing values (none), clarity distribution |
| **3. Data Cleaning** | Enforces dtypes, removes 20,584 duplicate rows → 33,356 clean rows |
| **4. Summary Statistics** | Mean/std of carat and price grouped by clarity; stacked bar chart of record counts |
| **5. Exploratory Visualizations** | Box plots per clarity (pre and post outlier removal); IQR-based outlier removal per group → `df_filtered` (30,784 rows) |
| **6. Relationships** | Correlation heatmap (r = 0.891); scatter plot of price vs. carat colored by clarity; markdown analysis of overlap zones |
| **7. Business Questions** | Answers to Q1 (top 3 clarity by median price) and Q2 (overlapping mean price/carat pairs) with full derivation in markdown |
| **8. Summary** | Results table summarizing key figures from the entire analysis |

---

## Key Findings

### Top 3 Clarity Categories by Highest Median Price

| Rank | Clarity | Median Price |
|------|---------|-------------|
| 1 | **SI2** | $3,965 |
| 2 | **SI1** | $3,795 |
| 3 | **VS2** | $3,756 |

*Note: SI1/SI2 rank above premium grades (IF, VVS1) because they tend to be larger in carat weight,
and carat is the dominant price driver (r = 0.891).*

### Overlapping Mean Price/Carat Combinations

Three clarity pairs exhibit overlapping mean profiles (within ±5% tolerance):

| Pair | Overlap Type |
|------|-------------|
| **SI1 & SI2** | Both mean price (~$4,100) and mean carat (~0.90–0.99) nearly identical — strongest mispricing zone |
| **SI1 & VS2** | Identical mean carat (~0.90 ct); VS2 carries ~17% price premium |
| **VS1 & VVS2** | Mean prices within 0.3% ($4,554 vs $4,566); VVS2 achieves same price with fewer carats |

---

## Libraries Used

| Library | Version | Purpose |
|---------|---------|---------|
| pandas | ≥ 1.5 | Data loading, cleaning, aggregation |
| numpy | ≥ 1.23 | Numeric operations |
| matplotlib | ≥ 3.6 | Base plotting |
| seaborn | ≥ 0.12 | Statistical visualizations |
| jupyter / nbconvert | ≥ 6.x | Notebook execution and HTML export |
