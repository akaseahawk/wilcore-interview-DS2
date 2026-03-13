# Diamond Pricing - Exploratory Data Analysis

EDA of the **P2-Mispriced-Diamonds** dataset for the [Wilcore Technologies Data Scientist 2 challenge](https://workforcenow.adp.com/mascsr/default/mdf/recruitment/recruitment.html?cid=f7dc51cf-bcd4-4d90-8703-f151ed980046&ccId=9201427462023_3&lang=en_US&jobId=559006&jwId=9201427462023_1).

Full requirements are in [Request.md](https://github.com/akaseahawk/wilcore-interview-DS2/blob/main/Request.md).

---

## Quick Start

```bash
git clone https://github.com/akaseahawk/wilcore-interview-DS2.git
cd wilcore-interview-DS2
pip install pandas numpy matplotlib seaborn jupyter
jupyter notebook diamond_analysis.ipynb
```

No Jupyter? Open `diamond_analysis.html` in any browser to view the fully executed notebook with all outputs and charts rendered.

---

## Repository Contents

| File | Description |
|------|-------------|
| `P2-Mispriced-Diamonds.csv` | Raw dataset - 53,940 rows, 3 columns (carat, clarity, price) |
| `diamond_analysis.ipynb` | Main analysis notebook, fully executed with all outputs |
| `diamond_analysis.html` | Static HTML export - no Jupyter required to view |
| `Request.md` | Original challenge requirements |

---

## Notebook Walkthrough

**Section 1 - Ingestion**  
Reads the CSV and confirms the load.

**Section 2 - Discovery**  
Reports shape, dtypes, missing value counts, and clarity distribution. No missing values were found; `clarity` was the only column requiring a type change (object to category).

**Section 3 - Data Cleaning**  
- `carat` cast to float64, `price` cast to int64, `clarity` cast to category  
- 20,584 exact duplicate rows removed, leaving 33,356 clean rows  
- Post-dedup null check (zero nulls confirmed)

**Section 4 - Summary Statistics**  
Mean and standard deviation for `carat` and `price` grouped by `clarity`. Accompanied by a stacked bar chart showing how each clarity grade distributes across carat-weight brackets (0-0.5 ct, 0.5-1 ct, etc.) - a more informative view than a plain count chart given only one categorical exists in the dataset.

**Section 5 - Exploratory Visualizations**  
Box plots show quantiles and outliers for price and carat per clarity grade. Outliers are then removed using the IQR method applied independently per clarity group (not globally), producing **`df_filtered`** with 30,784 rows. Per-group removal is intentional: IF and VVS1 diamonds are inherently smaller, so a global threshold would incorrectly trim valid large SI-grade stones.

**Section 6 - Relationships in Filtered Data**  
Pearson correlation heatmap (carat vs price r = 0.891). Scatter plot of price vs. carat colored by clarity reveals substantial overlap between adjacent grades - the primary source of mispricing in the dataset.

**Section 7 - Business Questions**  
Answers derived from a `support_report` DataFrame (mean price and mean carat per clarity on `df_filtered`). Each answer includes a markdown explanation of the derivation and interpretation.

**Section 8 - Price-per-Carat Analysis**  
Derives a `price_per_carat` feature (price / carat) to isolate the clarity premium from size effects. Raw median price incorrectly ranks SI1/SI2 highest because those stones tend to be larger. Price-per-carat correctly surfaces VVS2, VS1, and VS2 at the top, reflecting the true clarity premium hierarchy. Also reveals that VVS1 trades at a lower price-per-carat than SI1 - a genuine mispricing anomaly in the data.

---

## Assumptions

**Duplicates are artifacts, not repeated observations.**  
38% of the raw dataset (20,584 rows) are exact duplicates across all three columns. These are treated as data-entry errors and removed. If repeat observations were intentional (e.g., the same diamond appraised multiple times), the cleaning step would need revision.

**IQR outlier removal is applied per clarity group.**  
Each clarity grade has its own carat and price distribution. A global IQR threshold would be too aggressive for high-clarity grades and too lenient for lower-clarity ones. Per-group removal is the statistically sound choice here.

**Clarity is treated as nominal for EDA, ordinal for interpretation.**  
All grouping, coloring, and visualization treats clarity as a nominal category. Axis ordering follows the natural grade progression (I1 lowest, IF highest) for readability, but no numeric encoding is applied in the EDA sections.

**"Mispriced" refers to overlap zones between clarity tiers.**  
The dataset name implies pricing anomalies. The analysis targets zones where adjacent clarity grades command indistinguishable average prices for the same carat weight - most notably SI1/SI2 and VS1/VVS2.

---

## Dependencies

| Library | Purpose |
|---------|---------|
| `pandas` | Data loading, cleaning, groupby aggregations |
| `numpy` | Numeric operations |
| `matplotlib` | Base plotting and tick formatting |
| `seaborn` | Statistical visualizations (boxplots, heatmap, scatter) |
| `jupyter` | Notebook environment |
| `nbconvert` | HTML export (optional) |
