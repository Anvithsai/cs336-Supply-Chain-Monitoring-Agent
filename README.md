# 🚚 Supply Chain Monitoring Agent
### AIML Lab Assignment — AI Agent Design with Pipeline Architecture

An end-to-end AI pipeline that predicts supply chain delivery delays and generates intelligent, rule-based recommendations for every order using the **DataCo Supply Chain Dataset** from Kaggle.

---

## 📌 Problem Statement

Over 54.8% of orders in the DataCo dataset are delivered late, causing significant operational and financial losses. This project builds an **AI-based Supply Chain Monitoring Agent** that:

- Cleans and preprocesses raw logistics data
- Engineers meaningful delay-related features
- Trains a machine learning model to predict delivery delay probability
- Assigns risk levels (Low / Medium / High) to every order
- Generates actionable recommendations to prevent delays before they happen

---

## 📂 Project Structure

```
cs336-Supply-Chain-Monitoring-Agent/
│
├── Datasets/
│   ├── DataCoSupplyChainDataset.csv  ← Original Kaggle dataset (you provide this)
│   ├── cleaned_data.csv              ← Output of Notebook 1  [180,519 rows × 46 cols]
│   ├── processed_data.csv            ← Output of Notebook 2  [180,519 rows × 28 cols]
│   ├── feature_cols.json             ← Feature list saved by Notebook 2
│   ├── predictions.csv               ← Output of Notebook 3  [180,519 rows × 31 cols]
│   ├── final_output.csv              ← Output of Notebook 4  [180,519 rows × 10 cols]
│   └── dashboard.png                 ← Executive dashboard saved by Notebook 5
│
├── models/
│   └── delay_model.pkl               ← Trained Random Forest model (saved by Notebook 3)
│
├── notebooks/
│   ├── 1_data_preprocessing.ipynb
│   ├── 2_feature_engineering.ipynb
│   ├── 3_delay_prediction.ipynb
│   ├── 4_agent_logic.ipynb
│   └── 5_visualization.ipynb
│
├── README.md
└── presentation.pptx
```

---

## 📓 Notebook Descriptions

| # | Notebook | Purpose |
|---|---|---|
| 1 | `1_data_preprocessing.ipynb` | Load raw CSV, inspect schema, visualise distributions, handle missing values, fix dtypes, save clean data |
| 2 | `2_feature_engineering.ipynb` | Compute delay features, extract time features, create financial ratios, encode categoricals, save feature list |
| 3 | `3_delay_prediction.ipynb` | Train Random Forest + Logistic Regression, evaluate with ROC/PR curves, generate delay probabilities |
| 4 | `4_agent_logic.ipynb` | Map probabilities to risk levels, apply rule-based agent to assign recommendations per order |
| 5 | `5_visualization.ipynb` | Risk distribution charts, shipping mode analysis, executive KPI dashboard, final insights |

---

## 📊 Dataset

**Name:** DataCo Smart Supply Chain for Big Data Analysis  
**Source:** [https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis](https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis)  
**Raw size:** 180,519 rows × 53 columns

**Key columns used across the pipeline:**

| Column | Description |
|---|---|
| `Days for shipping (real)` | Actual number of days taken to ship |
| `Days for shipment (scheduled)` | Originally scheduled shipping days |
| `Late_delivery_risk` | Binary target — 1 = late delivery, 0 = on time |
| `Delivery Status` | Text status: Late Delivery, Advance Shipping, etc. |
| `Benefit per order` / `Sales per customer` | Financial metrics |
| `Order Profit Per Order` | Profitability per order |
| `Shipping Mode` | Standard Class, First Class, Second Class, Same Day |
| `Order Region` / `Market` | Geographic identifiers |
| `Category Name` / `Department Name` | Product classification |
| `order date (DateOrders)` | Order placement timestamp |
| `shipping date (DateOrders)` | Shipping timestamp |

## 📥 Getting the Dataset

The dataset (~180 MB) exceeds GitHub's file size limit and is **not included** in this repository. You must download it manually before running any notebook.

**Step 1 — Download**  
Visit the Kaggle page and download the dataset:  
👉 https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis

You will need a free Kaggle account. Click **Download** on the dataset page.

**Step 2 — Create the Datasets folder**  
The `Datasets/` folder is not tracked by Git. Create it manually inside the project root:

```bash
mkdir Datasets
```

Or create a new folder named `Datasets` in the project root using your file explorer.

**Step 3 — Place the file**  
Move `DataCoSupplyChainDataset.csv` (no renaming needed) into the folder you just created:

```
cs336-Supply-Chain-Monitoring-Agent/
└── Datasets/
    └── DataCoSupplyChainDataset.csv    ← file goes here
```

> ⚠️ The path `Datasets/DataCoSupplyChainDataset.csv` is hardcoded in Notebook 1. If the file is placed anywhere else or the folder does not exist, you will get a `FileNotFoundError` on the first cell.

> ⚠️ The dataset uses `latin-1` encoding. Notebook 1 handles this automatically.

---

## 🔁 Pipeline Overview

```
Datasets/DataCoSupplyChainDataset.csv  (180,519 rows × 53 columns)
         │
         ▼
[Notebook 1] Data Inspection, Visualization & Cleaning
         │   • Drops 7 leaky/irrelevant columns
         │   • Standardises Delivery Status labels
         │   • Parses date columns to datetime
         │   • Removes duplicates and handles nulls
         ▼
Datasets/cleaned_data.csv      (180,519 rows × 46 columns)
         │
         ▼
[Notebook 2] Feature Engineering
         │   • Computes delay_gap, delay_severity, scheduled_days_bin
         │   • Extracts order_month, order_dayofweek, order_quarter, is_weekend_order
         │   • Creates profit_margin_ratio, revenue_per_item, high_discount_flag, high_value_order
         │   • Computes risk scores: shipping_mode_risk, market_risk_score
         │   • Computes historical late rates: category_late_rate, dept_late_rate, segment_late_rate
         │   • Label-encodes all categorical features; saves feature list to feature_cols.json
         ▼
Datasets/processed_data.csv    (180,519 rows × 28 columns)
Datasets/feature_cols.json     (26 feature names)
         │
         ▼
[Notebook 3] Delay Prediction Model
         │   • Trains Random Forest + Logistic Regression (baseline)
         │   • Evaluates with Accuracy, Precision, Recall, F1, ROC-AUC, Cross-Validation
         │   • Best model: Random Forest — Test Acc: 71.32% | Full-data Acc: 72.09%
         │   • Generates delay_probability + predicted_late for all 180,519 orders
         ▼
models/delay_model.pkl         (Random Forest + scaler + feature list bundled)
Datasets/predictions.csv       (180,519 rows × 31 columns)
         │
         ▼
[Notebook 4] Risk Scoring + AI Agent Logic
         │   • Maps delay_probability → risk_level (LOW / MEDIUM / HIGH)
         │   • Applies rule-based agent to generate a recommendation per order
         │   • Results: LOW 5.1% (9,165) | MEDIUM 62.3% (112,477) | HIGH 32.6% (58,877)
         │   • 171,354 orders flagged as needing action
         ▼
Datasets/final_output.csv      (180,519 rows × 10 columns)
         │
         ▼
[Notebook 5] Visualization & Analysis
             • Risk distribution pie + bar charts
             • Delay probability histogram by actual outcome
             • Shipping mode and market-wise breakdown
             • Agent recommendation summary chart
             • Executive KPI dashboard saved as dashboard.png
             • Final insights printed to console
```

---

## 📓 Detailed Notebook Descriptions

### `1_data_preprocessing.ipynb` — Data Inspection, Visualization & Cleaning

**Input:** `Datasets/DataCoSupplyChainDataset.csv` (53 columns, loaded with `encoding='latin-1'`)  
**Output:** `Datasets/cleaned_data.csv` (46 columns)

What this notebook does, step by step:

- **Section 0:** Imports pandas, numpy, matplotlib, seaborn; configures plot style (`whitegrid`, `muted` palette) and sets `figure.figsize = (12, 5)` and `dpi = 100`
- **Section 1:** Loads the raw CSV (180,519 × 53); prints shape; displays first 3 rows including key columns like `Days for shipping (real)`, `Late_delivery_risk`, `Shipping Mode`
- **Section 2:** Explores data types, missing values per column, and distribution of the binary target variable `Late_delivery_risk`
- **Section 3:** Visualises key patterns — delivery status breakdown (bar chart), shipping mode distribution, late delivery rate by region, numeric feature correlation heatmap
- **Section 4:** Drops 7 columns that are irrelevant, data-leaky, or non-informative (e.g. `Product Description`, `Product Image`, `Order Zipcode`, `Customer Password`)
- **Section 5:** Cleans the data — standardises `Delivery Status` text labels (e.g. `"Late delivery"` → `"Late Delivery"`); parses `order date (DateOrders)` and `shipping date (DateOrders)` to proper datetime format; fills or removes remaining nulls
- **Section 6 (Cleaning Summary):**

| Metric | Value |
|---|---|
| Original shape | 180,519 rows × 53 columns |
| Cleaned shape | 180,519 rows × 46 columns |
| Columns removed | 7 |
| Missing values remaining | 0 |
| Duplicate rows | 0 |

---

### `2_feature_engineering.ipynb` — Feature Engineering

**Input:** `Datasets/cleaned_data.csv` (180,519 × 46)  
**Output:** `Datasets/processed_data.csv` (180,519 × 28) + `Datasets/feature_cols.json`

What this notebook does, step by step:

- **Section 0:** Imports pandas, numpy, matplotlib, seaborn, and scikit-learn's `LabelEncoder` and `StandardScaler`
- **Section 1:** Loads `cleaned_data.csv`; confirms 180,519 × 46 shape
- **Section 2 — New features created (26 total):**

| Feature | Type | Description |
|---|---|---|
| `delay_gap` | Numeric | `Days_shipping_real` − `Days_shipment_scheduled` (positive = late) |
| `delay_severity` | Categorical | Severity bucket based on delay_gap magnitude |
| `scheduled_days_bin` | Ordinal | Binned scheduled shipping days |
| `order_month` | Numeric | Month extracted from order date |
| `order_dayofweek` | Numeric | Day of week (0=Mon, 6=Sun) |
| `order_quarter` | Numeric | Quarter of the year |
| `is_weekend_order` | Binary | 1 if order placed on Saturday or Sunday |
| `profit_margin_ratio` | Numeric | Profit ÷ Sales per order |
| `revenue_per_item` | Numeric | Sales ÷ Order Item Quantity |
| `high_discount_flag` | Binary | 1 if discount rate exceeds threshold |
| `high_value_order` | Binary | 1 if sales exceed high-value threshold |
| `shipping_mode_risk` | Numeric | Historical late-delivery rate per shipping mode |
| `market_risk_score` | Numeric | Historical late-delivery rate per market |
| `category_late_rate` | Numeric | Historical late-delivery rate per product category |
| `dept_late_rate` | Numeric | Historical late-delivery rate per department |
| `segment_late_rate` | Numeric | Historical late-delivery rate per customer segment |
| `ship_mode_x_sched` | Numeric | Interaction: shipping_mode_risk × scheduled days |
| `shipping_mode_enc` | Encoded | Label-encoded Shipping Mode |
| `market_enc` | Encoded | Label-encoded Market |
| `customer_segment_enc` | Encoded | Label-encoded Customer Segment |
| `department_name_enc` | Encoded | Label-encoded Department Name |
| `category_name_enc` | Encoded | Label-encoded Category Name |
| `order_status_enc` | Encoded | Label-encoded Order Status |

- **Section 3:** Defines `FEATURE_COLS` (26 features) and `TARGET_COL = 'Late_delivery_risk'`
- **Section 4:** Plots feature correlation with target as a horizontal bar chart (orange = positive correlation, blue = negative)
- **Section 5 (Save):** Saves `processed_data.csv` (Order Id + 26 features + target = 28 cols) and `feature_cols.json` (list of 26 feature names for Notebook 3 to read)

---

### `3_delay_prediction.ipynb` — Delay Prediction Model

**Input:** `Datasets/processed_data.csv` + `Datasets/feature_cols.json`  
**Output:** `models/delay_model.pkl` + `Datasets/predictions.csv`

What this notebook does, step by step:

- **Section 0:** Imports scikit-learn classifiers (`RandomForestClassifier`, `GradientBoostingClassifier`, `LogisticRegression`), metrics, `pickle`, `json`, `os`; creates `models/` directory with `os.makedirs`
- **Section 1:** Loads processed data (180,519 × 28); reads feature list from `feature_cols.json`; prints target balance — Late: 98,977 | On-time: 81,542
- **Section 2:** Splits data — 80% train / 20% test with `train_test_split(stratify=y, random_state=42)`; applies `StandardScaler` on all features
- **Section 3:** Trains and compares two models with 5-fold `StratifiedKFold` cross-validation:
  - **Logistic Regression** — baseline model
  - **Random Forest Classifier** — primary model, selected as best
- **Section 4 — Evaluation of best model (Random Forest):**
  - Confusion matrix heatmap (seaborn)
  - ROC curve with AUC score
  - Full classification report (Precision, Recall, F1 per class)
  - Predicted probability distribution histogram — on-time vs late orders overlaid
- **Section 5 — Save outputs:**
  - `delay_model.pkl` — pickled dictionary containing: `model`, `model_name`, `feature_cols`, `scaler`
  - `predictions.csv` — full dataset (all 180,519 rows) enriched with three new columns: `predicted_late`, `delay_probability`, `prediction_correct`

---

### `4_agent_logic.ipynb` — Risk Scoring + AI Agent Logic

**Input:** `Datasets/predictions.csv` (180,519 × 31)  
**Output:** `Datasets/final_output.csv` (180,519 × 10)

What this notebook does, step by step:

- **Section 0:** Imports pandas, numpy, matplotlib, seaborn; sets plot theme
- **Section 1:** Loads predictions file; confirms 180,519 × 31 shape; previews `delay_probability` statistics (mean=0.516, min=0.015, max=0.999)
- **Section 2 — Risk Scoring:** Defines thresholds (`LOW_THRESHOLD = 0.30`, `HIGH_THRESHOLD = 0.70`) and maps every order's `delay_probability` to a `risk_level`:

| Delay Probability | Risk Level | Count | Share |
|---|---|---|---|
| < 0.30 | LOW | 9,165 | 5.1% |
| 0.30 – 0.70 | MEDIUM | 112,477 | 62.3% |
| > 0.70 | HIGH | 58,877 | 32.6% |

- **Section 3 — Agent Rule Engine:** Applies multi-condition if-else rules to assign a human-readable `recommendation` to each order. Key rules:

| Condition | Recommendation |
|---|---|
| risk_level = HIGH | 🔴 High Delay Risk: Switch to Faster Shipping Mode |
| risk_level = MEDIUM + high_discount_flag = 1 | 🟡 Discounted Order with Moderate Risk: Verify Fulfilment |
| risk_level = MEDIUM (general) | 🟡 Moderate Delay Risk: Flag for Supervisor Review |
| risk_level = LOW | 🟢 Low Risk: No Immediate Action Required |

- **Section 4:** Validates agent outputs — bar chart of actual late rate within each risk bucket; horizontal bar chart of all recommendation types and their order counts
- **Section 5 — Save:** Saves `final_output.csv` with 10 interpretable columns: `Order Id`, `Sales`, `Order Profit Per Order`, `Order Item Quantity`, `Days for shipment (scheduled)`, `Late_delivery_risk`, `delay_probability`, `predicted_late`, `risk_level`, `recommendation`

**Agent Summary (printed at end of notebook):**

```
Total orders processed :  180,519
HIGH risk orders       :   58,877
MEDIUM risk orders     :  112,477
LOW risk orders        :    9,165
Orders needing action  :  171,354
```

---

### `5_visualization.ipynb` — Visualization & Analysis

**Input:** `Datasets/final_output.csv` + `Datasets/predictions.csv`  
**Output:** Charts rendered inline + `Datasets/dashboard.png` (no new CSV produced)

What this notebook does, step by step:

- **Section 0:** Imports pandas, numpy, matplotlib (`GridSpec` for dashboard layout), seaborn; defines project color palette — LOW: `#4CAF82` (green), MEDIUM: `#F4C542` (yellow), HIGH: `#E07B54` (orange)
- **Section 1:** Loads `final_output.csv` (180,519 × 13 with merged columns); previews all columns including `order_month`, `shipping_mode_risk`, `market_risk_score`
- **Section 2:** Risk distribution — pie chart and bar chart showing LOW / MEDIUM / HIGH order counts
- **Section 3:** Delay probability histogram — overlaid distributions for on-time vs late orders to visualise model separation
- **Section 4:** Shipping mode analysis — late delivery rate per mode; risk score comparison across Standard Class, First Class, Second Class, Same Day
- **Section 5:** Market/region analysis — delay rates grouped by `market_risk_score`
- **Section 6:** Agent recommendation breakdown — horizontal bar chart showing the count of every unique recommendation generated
- **Section 7 — Executive KPI Dashboard** saved as `Datasets/dashboard.png` (18×10 inches, 120 DPI):
  - **Top row — 4 KPI tiles:** Total Orders | Model Accuracy | HIGH Risk Orders | Avg Delay Probability
  - **Bottom left:** Risk distribution pie chart (LOW / MEDIUM / HIGH)
  - **Bottom right:** Delay gap or delay probability histogram with decision boundary line
- **Section 8 — Final Insights (printed to console):**
  1. 54.8% of all orders are delivered late — a significant operational challenge
  2. The model achieves 72.1% accuracy — a reliable early-warning system
  3. 32.6% of orders are flagged HIGH risk, requiring immediate action
  4. Late orders carry an average delay probability of 64.9%
  5. The agent's rule engine categorises every order into actionable buckets — eliminating manual review bottlenecks

---

## ▶️ Execution Order

**Run notebooks strictly in this sequence. Do not skip steps.**

```
Step 1 → 1_data_preprocessing.ipynb    reads DataCoSupplyChainDataset.csv → writes cleaned_data.csv
Step 2 → 2_feature_engineering.ipynb   reads cleaned_data.csv   → writes processed_data.csv + feature_cols.json
Step 3 → 3_delay_prediction.ipynb      reads processed_data.csv → writes delay_model.pkl + predictions.csv
Step 4 → 4_agent_logic.ipynb           reads predictions.csv    → writes final_output.csv
Step 5 → 5_visualization.ipynb         reads final_output.csv   → writes dashboard.png + prints insights
```

> ⚠️ **Critical:** Each notebook reads the output of the previous one. Running out of order or skipping a step will cause a `FileNotFoundError`. Always complete each notebook fully (all cells run, no errors) before opening the next.

---

## 🏆 Key Results

| Metric | Value |
|---|---|
| Best Model | Random Forest |
| Test Accuracy | 71.32% |
| Full-data Accuracy | 72.09% |
| Train-Test Gap | 0.0096 (no significant overfitting) |
| ROC-AUC | evaluated per notebook 3 output |
| HIGH risk orders | 58,877 (32.6%) |
| Orders needing action | 171,354 (94.9%) |
| Avg delay probability (late orders) | 64.9% |

---

## 🛠️ Software Environment

### Python Version

```
Python 3.14.3  (used during development)
Compatible with Python 3.9 and above
```

### Required Libraries

| Library | Version Used | Role in Project |
|---|---|---|
| `pandas` | 2.x | Data loading, manipulation, CSV I/O |
| `numpy` | 1.x / 2.x | Numerical array operations |
| `matplotlib` | 3.7.x | Plotting, GridSpec dashboard layout |
| `seaborn` | 0.12.x | Statistical visualizations, heatmaps |
| `scikit-learn` | 1.2.x+ | RandomForest, LogisticRegression, metrics, StandardScaler, train_test_split, StratifiedKFold |
| `pickle` | stdlib (built-in) | Saving and loading the trained model |
| `json` | stdlib (built-in) | Saving and loading the feature column list |
| `warnings` | stdlib (built-in) | Suppressing non-critical runtime warnings |
| `jupyter` | 1.0.0 | Notebook runtime environment |

### Installation

**Option A — pip (recommended for quick setup)**

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

**Option B — conda**

```bash
conda create -n supply-chain-agent python=3.10
conda activate supply-chain-agent
conda install pandas numpy matplotlib seaborn scikit-learn jupyter
```

**Option C — requirements file**

Create `requirements.txt`:

```
pandas>=1.5.3
numpy>=1.24.0
matplotlib>=3.7.0
seaborn>=0.12.0
scikit-learn>=1.2.0
jupyter>=1.0.0
```

Then run:

```bash
pip install -r requirements.txt
```

---

## 🚀 How to Run

### Option 1: Jupyter Notebook (Recommended)

```bash
# 1. Clone the repository
git clone https://github.com/Anvithsai/cs336-Supply-Chain-Monitoring-Agent
cd cs336-Supply-Chain-Monitoring-Agent

# 2. Install all dependencies
pip install pandas numpy matplotlib seaborn scikit-learn jupyter

# 3. Create required directories
mkdir Datasets
mkdir models

# 4. Download the dataset from Kaggle, place DataCoSupplyChainDataset.csv in Datasets/
#    See "Getting the Dataset" section above for full instructions

# 5. Launch Jupyter
jupyter notebook

# 6. Open the notebooks/ folder in the browser
#    Run each notebook top-to-bottom in order: 1 → 2 → 3 → 4 → 5
#    Confirm the ✅ message at the end of each before moving to the next
```

### Option 2: VS Code with Jupyter Extension

1. Open the `cs336-Supply-Chain-Monitoring-Agent/` folder in VS Code
2. Install the **Jupyter** extension from the Extensions panel
3. Open `notebooks/1_data_preprocessing.ipynb` and click **Run All**
4. Confirm the notebook prints `✅ Cleaned data saved to: ../Datasets/cleaned_data.csv`
5. Repeat for notebooks 2, 3, 4, and 5 in order

### Option 3: JupyterLab

```bash
pip install jupyterlab
jupyter lab
# Navigate to notebooks/ and run files in order 1 → 5
```

---

## 🤖 Agent Output Sample

After running Notebook 4, `final_output.csv` contains one row per order. Sample rows:

| Order Id | Sales | Order Profit Per Order | Days Scheduled | Late_delivery_risk | delay_probability | risk_level | recommendation |
|---|---|---|---|---|---|---|---|
| 77202 | 327.75 | 91.25 | 4 | 0 | 0.3719 | MEDIUM | 🟡 Moderate Delay Risk: Flag for Supervisor Review |
| 75939 | 327.75 | −249.09 | 4 | 1 | 0.3388 | MEDIUM | 🟡 Moderate Delay Risk: Flag for Supervisor Review |
| 52224 | 199.99 | 45.00 | 3 | 1 | 0.9924 | HIGH | 🔴 High Delay Risk: Switch to Faster Shipping Mode |
| 168372 | 89.99 | 22.10 | 4 | 0 | 0.4123 | MEDIUM | 🟡 Discounted Order with Moderate Risk: Verify Fulfilment |

---

## ⚖️ Ethical & Societal Relevance

- **Transparency:** All agent recommendations are rule-based and fully auditable. Every decision traces back to an explicit probability threshold and a named condition — no black-box outputs.
- **Bias Awareness:** The model uses historical late-delivery rates per region, market, and product category as features. These encode real patterns but may also reflect structural disparities. Results should be reviewed periodically to ensure no region or segment is systematically penalised.
- **Fairness:** Risk scoring applies uniform probability thresholds across all orders regardless of customer identity or location, ensuring consistent treatment.
- **Data Privacy:** Customer names, emails, passwords, and other personally identifiable information are dropped in Notebook 1 and are never used in modelling.
- **Societal Benefit:** Reducing supply chain delays improves reliability for end consumers, particularly in healthcare, food, and essential goods sectors. Smarter re-routing also reduces unnecessary expedited shipments, contributing to lower carbon emissions.
- **Economic Impact:** Proactive delay prediction enables intervention before shipments fail, protecting business revenue, preventing financial penalties, and maintaining customer trust.

---

## 📎 References

- DataCo Supply Chain Dataset (Kaggle): https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis
- Scikit-learn Documentation: https://scikit-learn.org/stable/
- Pandas Documentation: https://pandas.pydata.org/docs/
- Matplotlib Documentation: https://matplotlib.org/stable/
- Seaborn Documentation: https://seaborn.pydata.org/
