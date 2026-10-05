# OTT Customer Churn Analysis

An end-to-end exploratory analysis of customer churn for an OTT (streaming) subscription service, built in Python with a Jupyter Notebook. The project pulls data from a SQLite database, cleans and merges three tables, engineers a churn flag, computes key business KPIs, and visualizes the drivers of churn.

## Project Objectives

- Load and combine customer, subscription and support data from a SQLite database
- Clean and standardize the data (types, naming, missing values, duplicates)
- Engineer features such as `churn_flag`, `tenure_days`, `complaint_count` and `churn_risk`
- Calculate churn-related KPIs (churn rate, retention, ARPU, tenure, revenue at risk, escalation rate)
- Visualize churn patterns by time, plan type, state and other features

## Repository Structure

```
OTT_Churn_Analysis/
├── churn_analysis.ipynb        # Main analysis notebook
├── customer_churn.db           # SQLite database (raw data)
├── exported_churn_data.csv     # Cleaned, merged dataset exported from the notebook
├── images/                     # Charts used in this README
├── requirements.txt            # Python dependencies
├── .gitignore
└── README.md
```

## Dataset

The SQLite database `customer_churn.db` contains three tables:

| Table | Description | Key columns |
|-------|-------------|-------------|
| `db_customer` | Customer demographics | `customerid`, `name`, `country`, `state`, `gender`, `dob` |
| `db_subscription` | Subscription details | `customerid`, `subscription_start_date`, `plan_type`, `contract_type`, `cancellation_date`, `monthly_charges`, `cltv`, `churn_score` |
| `db_support` | Support complaints | `customerid`, `complaint_date`, `escalations`, `csat_score` |

The cleaned and merged output (21 rows, 21 columns) is saved as `exported_churn_data.csv`.

## Methodology

**1. Data import**: Connected to SQLite, listed all tables and loaded each into its own DataFrame.

**2. Data cleaning**
- Renamed `name` to `customer_name`
- Dropped unused columns (`interests`, `pincode`, `col_1`, `comment`)
- Converted `dob`, `subscription_start_date`, `renewal_date`, `cancellation_date` and `complaint_date` to datetime
- Standardized gender values (`Men`/`Women` to `Male`/`Female`)
- Filled missing `country` values using a state-to-country mapping built from existing rows

**3. Feature engineering**
- `churn_flag`: 1 if `cancellation_date` is present, otherwise 0
- `complaint_count`: number of complaints per customer
- Support table de-duplicated to the latest complaint per customer **before** merging, so the merge doesn't create duplicate rows (row counts were checked before and after)
- `tenure_days`: days from subscription start to cancellation (or to today if still active)
- `churn_risk`: `low` (score < 50), `med` (50 to 69), `high` (70+) based on `churn_score`

**4. Analysis and visualization**: KPIs, Matplotlib charts, Seaborn heatmap, pairplot, catplot and pivot tables. The notebook also demonstrates why ordinal encoding (Basic < Standard < Premium) is more appropriate than arbitrary category codes for correlation analysis.

## Key Results

| KPI | Value |
|-----|-------|
| Churn rate | **28.57%** |
| Retention rate | **71.43%** |
| ARPU (avg monthly charges) | **18.85** |
| Average tenure | **~1,554 days** |
| Revenue at risk (monthly charges of churned users) | **73.94** |
| Escalation rate | **19.05%** |
| Avg complaints per user | **0.43** |
| Correlation: escalation vs churn | **0.77** |

**Churn rate by plan type**

| Plan | Churn rate |
|------|-----------|
| Basic | 60.00% |
| Standard | 22.22% |
| Premium | 14.29% |

### Visuals

| Monthly churn trend | Churn rate by plan type |
|---|---|
| ![Monthly churn trend](images/monthly_churn_trend.png) | ![Churn by plan](images/churn_by_plan.png) |

| Churn rate by state | Correlation heatmap |
|---|---|
| ![Churn by state](images/churn_by_state.png) | ![Correlation heatmap](images/correlation_heatmap.png) |

### Insights

- Basic plan customers churn far more than Standard and Premium customers.
- Escalated complaints show a strong positive correlation with churn.
- Monthly contracts and lower-tier plans are associated with higher churn scores.

> **Note:** The dataset is small (21 customers), so these figures are illustrative. Patterns should be validated on a larger dataset before drawing business conclusions.

## Getting Started

```bash
# 1. Clone the repository
git clone https://github.com/nikhilasoliya00/OTT_Churn_Analysis.git
cd OTT_Churn_Analysis

# 2. (Optional) create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch the notebook
jupyter notebook churn_analysis.ipynb
```

Run the notebook from the repository root so it can find `customer_churn.db`.

## Tech Stack

Python, Pandas, NumPy, Matplotlib, Seaborn, SQLite, Jupyter Notebook

## Author

**Nikhil Asoliya**: [GitHub](https://github.com/nikhilasoliya00)
