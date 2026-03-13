# Diamond Pricing — Exploratory Data Analysis & Predictive Modeling

EDA and predictive pricing analysis of the **P2-Mispriced-Diamonds** dataset for the Wilcore Technologies Data Scientist 2 challenge.

---

## Quick Start

```bash
git clone https://github.com/akaseahawk/wilcore-interview-DS2.git
cd wilcore-interview-DS2
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
jupyter notebook diamond_analysis.ipynb
```

No Jupyter? Open `diamond_analysis.html` in any browser to view the fully executed notebook with all outputs and charts rendered.

---

## Repository Contents

| File | Description |
|------|-------------|
| `P2-Mispriced-Diamonds.csv` | Raw dataset — 53,940 rows, 3 columns (carat, clarity, price) |
| `diamond_analysis.ipynb` | Main analysis notebook, fully executed with all outputs |
| `diamond_analysis.html` | Static HTML export — no Jupyter required to view |
| `Request.md` | Original challenge requirements |

---

## Notebook Walkthrough

**Section 1 — Ingestion**  
Reads the CSV and confirms the load.

**Section 2 — Discovery**  
Reports shape, dtypes, missing value counts, and clarity distribution. No missing values were found; `clarity` was the only column requiring a type change (object → category).

**Section 3 — Data Cleaning**  
- `carat` → `float64`, `price` → `int64`, `clarity` → `category`  
- 20,584 exact duplicate rows removed → **33,356 clean rows**  
- Post-dedup null check (zero nulls confirmed)

**Section 4 — Summary Statistics**  
Mean and standard deviation for `carat` and `price` grouped by `clarity`. Accompanied by a stacked bar chart showing how each clarity grade distributes across carat-weight brackets (0–0.5 ct, 0.5–1 ct, etc.) — a more informative view than a plain count chart given only one categorical exists in the dataset.

**Section 5 — Exploratory Visualizations**  
Box plots show quantiles and outliers for price and carat per clarity grade. Outliers are then removed using the IQR method applied independently per clarity group (not globally), producing **`df_filtered`** — 30,784 rows. Per-group removal is intentional: IF and VVS1 diamonds are inherently smaller, so a global threshold would incorrectly trim valid large SI-grade stones.

**Section 6 — Relationships in Filtered Data**  
Pearson correlation heatmap (carat ↔ price r = 0.891). Scatter plot of price vs. carat colored by clarity reveals substantial overlap between adjacent grades — the primary source of mispricing in the dataset.

**Section 7 — Business Questions**  
Answers derived from a `support_report` DataFrame (mean price and mean carat per clarity on `df_filtered`). Each answer includes a markdown explanation of the derivation and interpretation.

**Section 8 — Price-per-Carat Analysis**  
Derives a `price_per_carat` feature (`price / carat`) to isolate the clarity premium from size effects. Raw median price incorrectly ranks SI1/SI2 highest (larger stones). Price-per-carat correctly surfaces **VVS2 → VS1 → VS2** at the top, reflecting the true clarity premium hierarchy. Also reveals that VVS1 trades at a lower price-per-carat than SI1 — a genuine mispricing anomaly in the data.

**Section 9 — Predictive Pricing Model**  
Compares three models using 5-fold cross-validation:

| Model | CV R² |
|-------|-------|
| Linear Regression (raw features) | 0.810 ± 0.066 |
| **Log-Log Linear Regression** | **0.942 ± 0.018** |
| Random Forest | 0.747 ± 0.129 |

The **Log-Log Linear Regression** is selected. Log-transforming both price and carat linearizes the multiplicative relationship (a 2 ct stone costs far more than 2× a 1 ct stone) and yields the best generalization. Random Forest's lower training RMSE is misleading — its high CV variance (±0.129) indicates overfitting on this 2-feature dataset. Model coefficients are interpreted as elasticities: each clarity grade step adds ~13% to price; a 10% heavier stone costs ~17% more.

**Section 10 — Mispricing Residual Analysis**  
Uses the trained model to define "mispriced" precisely: stones where actual price deviates significantly from the model's fair-value prediction. Tables rank the top 15 most underpriced and overpriced diamonds with actual price, predicted fair value, dollar gap, and % deviation. A per-clarity breakdown of mean absolute % deviation identifies which grades have the most pricing inconsistency — where appraisal scrutiny adds the most value.

---

## Assumptions

**Duplicates are artifacts, not repeated observations.**  
38% of the raw dataset (20,584 rows) are exact duplicates across all three columns. These are treated as data-entry errors and removed. If repeat observations were intentional (e.g., the same diamond appraised multiple times), the cleaning step would need revision.

**IQR outlier removal is applied per clarity group.**  
Each clarity grade has its own carat and price distribution. A global IQR threshold would be too aggressive for high-clarity grades and too lenient for lower-clarity ones. Per-group removal is the statistically sound choice here.

**Clarity is ordinally encoded for modeling only.**  
For EDA and visualization, clarity is treated as a nominal category and grouped by grade name. For the predictive model (Section 9), clarity is ordinally encoded (I1=0 → IF=7) to capture the natural quality progression as a numeric feature.

**"Mispriced" is operationalized as model residual.**  
The dataset name implies pricing anomalies. Rather than a subjective definition, Section 10 defines mispricing concretely as the signed difference between actual price and the log-log model's fair-value prediction, expressed in dollars and as a percentage.

---

## Dependencies

| Library | Purpose |
|---------|---------|
| `pandas` | Data loading, cleaning, groupby aggregations |
| `numpy` | Numeric operations and log transforms |
| `matplotlib` | Base plotting and tick formatting |
| `seaborn` | Statistical visualizations (boxplots, heatmap, scatter) |
| `scikit-learn` | Linear regression, random forest, cross-validation |
| `jupyter` | Notebook environment |
| `nbconvert` | HTML export (optional) |
