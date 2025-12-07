# 🚗 End-to-End Insurance Risk Analytics & Predictive Modeling
  
This repository contains the implementation for the **End-to-End Insurance Risk Analytics & Predictive Modeling**, focused on analyzing car insurance data for **AlphaCare Insurance Solutions (ACIS)**. The project aims to **optimize premiums** and **identify low-risk customer segments** using insurance policy data from **2014–2015**.

The solution leverages **Python**, **DVC**, and statistical analysis to deliver actionable insights through modular scripts, Jupyter notebooks, and rich visualizations.
 
---
 
## 🧾 Project Overview 

The dataset (`MachineLearningRating_v3.txt`) includes **1,000,098 records** with **52 columns**, covering:
- Insurance policies
- Client demographics
- Vehicle specifications
- Claim history

### 📌 Project Tasks:
- **Task 1:** Perform EDA focusing on loss ratios by **province**, **vehicle type**, and **time**.
- **Task 2:** Set up **Data Version Control (DVC)** for managing datasets and cleaning pipeline automation.

---

## 📁 Repository Structure

```plaintext
├── data/
│   ├── raw/                    # Raw dataset (MachineLearningRating_v3.txt)
│   └── processed/              # Cleaned dataset (clean_data.csv)
├── scripts/                    # Python scripts for processing and analysis
│   ├── data_loader.py          # Data loading and cleaning logic
│   ├── eda_analysis.py         # Modular EDA functions
│   └── run_data_cleaning.py    # DVC pipeline entry point
├── notebooks/
│   └── task_1_eda.ipynb        # Task 1 EDA notebook
├── plots/                      # Generated visualizations
├── docs/
│   └── task_1_summary.md       # Summary of findings
├── .github/workflows/
│   └── ci.yml                  # CI/CD with GitHub Actions
├── .dvc/                       # DVC configuration directory
├── .gitignore
├── dvc.yaml                    # DVC pipeline specification
├── dvc.lock                    # DVC pipeline lock file
├── requirements.txt
└── README.md
```

---

### 📊 1: Exploratory Data Analysis (EDA)

#### ✔️ Project Setup
- Initialized repo and virtual environment.
- Installed dependencies: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`.
- Configured CI/CD with GitHub Actions for linting and testing.
- Organized directories for scripts, notebooks, and data.

#### ✔️ Data Loading and Cleaning
- Built `DataLoader` class in `scripts/data_loader.py`.
- Handled delimiter detection, column renaming, and imputation:
  - **285K rows** with missing `totalpremium` imputed using `calculatedpremiumperterm`.
  - Missing `bank`, `accounttype`, `capitaloutstanding` filled with defaults.
  - Dropped sparse columns and **162K rows** with critical data missing.
- Output saved to: `data/processed/clean_data.csv`.

#### 🧠 Data Challenges:
- **Gender missing (~97%)**: Imputed with mode (Male), skipped deeper analysis due to unreliability.
- **Negative values**: Removed negative `totalpremium` and `totalclaims`.

#### ✔️ EDA and Visualizations
- Built `EDAAnalysis` class in `scripts/eda_analysis.py`.
- Performed detailed analysis in `notebooks/task_1_eda.ipynb`:
  - **Loss Ratios:**
    - Overall: ~0.231
    - By province: Gauteng (0.282), Limpopo (0.268), Northern Cape (0.116)
    - By vehicle type: Buses (0.938), Heavy Commercial (0.571), Passenger (0.221)
  - **Visualizations (saved in `plots/`):**
    - `loss_ratio_province.png`
    - `claims_vehicletype.png`
    - `temporal_trends.png`

#### 🔍 Key Insights
- Adjust premiums in high-risk areas and vehicle types.
- Target low-risk areas (e.g., Passenger vehicles, Northern Cape) for growth.
- Improve gender data for deeper demographic profiling.

---

### 📂 2: Data Version Control (DVC)

#### ✔️ Setup
- Installed DVC and added to `requirements.txt`.
- Initialized DVC: created `.dvc/`, committed config files to Git.

#### ✔️ Data Tracking
- Tracked:
  - `data/raw/MachineLearningRating_v3.txt`
  - `data/processed/clean_data.csv`
- Ensured `.gitignore` excludes actual data files (tracks only `.dvc` files).

#### ✔️ DVC Pipeline
- Built `scripts/run_data_cleaning.py` to clean raw data.
- Defined pipeline in `dvc.yaml`:
  - **Stage:** `clean_data`
  - **Command:** `python scripts/run_data_cleaning.py`
  - **Dependencies:** raw file, cleaning script, data loader
  - **Output:** cleaned CSV

- Ran `dvc repro` to:
  - Generate `dvc.lock`
  - Verify pipeline reproducibility

#### 📦 Deliverables
- `.dvc/`, `dvc.yaml`, `dvc.lock`
- `.dvc` tracked files for raw and cleaned data
- `scripts/run_data_cleaning.py` for pipeline execution

---

## 🛠️ Setup Instructions

```bash
# 1. Clone the repository
git clone https://github.com/your-username/insurance-analytics.git
cd insurance-analytics
git checkout task-2

# 2. Set up virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scriptsctivate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Install DVC
pip install dvc

# 5. Pull DVC-tracked datasets
dvc pull  # Requires DVC remote; otherwise, place datasets manually

# 6. Run the DVC pipeline
dvc repro

# 7. Run EDA notebook
jupyter notebook notebooks/task_1_eda.ipynb
```

---

## 📈 Usage Guide

- 🔁 **Reproduce EDA:** Run notebook `notebooks/task_1_eda.ipynb`
- 🧼 **Update Data:**
  ```bash
  dvc add data/raw/MachineLearningRating_v3.txt
  dvc repro
  ```
- 📊 **Visualizations:** Found in `plots/`
- 📚 **Findings Summary:** See `docs/task_1_summary.md`

---

## ⚠️ Challenges & Solutions

| Challenge | Resolution |
|-----------|-------------|
| **High number of zero `totalpremium` values (~285,696 rows)** | Imputed zeros in `impute_totalpremium` using `calculatedpremiumperterm`, adjusted for 14% VAT if `isvatregistered`, based on `termfrequency` ('Monthly' or 'Annual'). Added median imputation for remaining zeros (e.g., ~5 rows) to ensure no zeros persist. |
| **Ensuring cleaned data is saved correctly** | Implemented `save_cleaned_data` to save `self.data` to `data/processed/clean_data.csv` (updated from `../data/processed`), creating the directory with `os.makedirs`. Called within `clean_data` to persist imputed `totalpremium` and other changes. |
| **PEP8 line length violations (E501) in `impute_totalpremium`** | Split long print statements for diagnosis and sample imputed rows over multiple lines using parentheses, ensuring each line is ≤88 characters, as fixed in prior `data_loader.py` update. |
| **High gender missingness** | Imputed missing `gender` in `impute_gender_from_title` using title mappings (e.g., 'Mr' → 'Male', 'Ms' → 'Female'). Dropped ambiguous 'Dr' titles with missing gender and replaced 'Not specified' with `NaN` for consistency. |
| **Sparse columns with excessive missingness** | Dropped sparse columns (`customvalueestimate`, `writtenoff`, etc.) in `drop_sparse_columns` to reduce noise and improve modeling reliability. |
| **Missing vehicle-related data impacting analysis** | Dropped rows with missing critical vehicle columns (`mmcode`, `vehicletype`, etc.) in `drop_rows_with_missing_vehicle_info` to ensure data quality for A/B testing and modeling. |
---

## 3: A/B Hypothesis Testing for Risk Drivers

### Objective
Test four null hypotheses to identify risk drivers, supporting ACIS’s goal of optimizing premiums and targeting low-risk segments.

### Methodology
- **Metrics**:
  - Claim Frequency: Proportion of policies with `totalclaims` > 0.
  - Claim Severity: Mean `totalclaims` for claims > 0.
  - Margin: `totalpremium` - `totalclaims`.
- **Null Hypotheses**:
  - H₀: No risk differences across provinces (Gauteng vs. KwaZulu-Natal).
  - H₀: No risk differences between zip codes.
  - H₀: No significant margin difference between zip codes.
  - H₀: No significant risk difference between Women and Men.
- **Segmentation**:
  - Province: Gauteng vs. KwaZulu-Natal.
  - PostalCode: High-risk (top 20%, 170 postcodes) vs. low-risk (bottom 20%, 170 postcodes), filtered for >10 claims.
  - Gender: Female (6.5%) vs. Male (93.5%).
- **Tests**:
  - Chi-squared or Fisher’s exact for Claim Frequency.
  - Welch’s t-test for Claim Severity and Margin.
  - Equivalence checked for `maritalstatus`, `vehicletype`, `covertype`, `cubiccapacity`.
  - P-values adjusted via FDR.
- **Data Cleaning**:
  - Filtered negative `totalpremium`/`totalclaims`.
  - Dropped 5 zero `totalpremium` rows.
  - Gender: ~93.5% Male post-imputation.

### Findings
- **Province (Gauteng vs. KwaZulu-Natal)**:
  - Claim Frequency: p = 3.04e-03 (adjusted 4.26e-03), reject H₀. Gauteng has higher frequency.
  - Claim Severity: p = 3.20e-04 (adjusted 5.59e-04), reject H₀. Gauteng has higher severity.
  - **Limitation**: Vehicle differences (p = 3.74e-79 for `vehicletype`, 7.19e-72 for `cubiccapacity`) may confound results.
  - **Recommendation**: Increase Gauteng premiums by ~10-15%; target KwaZulu-Natal for low-risk marketing.
- **PostalCode (Risk)**:
  - Claim Frequency: p = 1.62e-14 (adjusted 1.13e-13), reject H₀. High-risk postcodes have higher frequency.
  - Claim Severity: p = 8.33e-01 (adjusted 8.38e-01), fail to reject H₀.
  - **Recommendation**: Implement postcode-based pricing for high-risk areas.
- **PostalCode (Margin)**:
  - Margin: p = 3.97e-07 (adjusted 1.39e-06), reject H₀. Low-risk postcodes more profitable.
  - **Recommendation**: Market to low-risk postcodes.
- **Gender**:
  - Claim Frequency: p = 4.21e-05 (adjusted 9.82e-05), reject H₀. Female higher frequency.
  - Claim Severity: p = 8.38e-01 (adjusted 8.38e-01), fail to reject H₀.
  - **Limitation**: ~93.5% Male imputation biases results.
  - **Recommendation**: Exclude gender; improve data collection.

### Business Impact
Targeted pricing in Gauteng and high-risk postcodes, and marketing to low-risk postcodes and KwaZulu-Natal, will enhance profitability. Gender data quality improvement is critical.

### Deliverables
- `scripts/data_loader.py`, `hypothesis_testing.py`
- `notebooks/task_3_hypothesis_testing.ipynb`
- `reports/hypothesis_test_results.csv`
- `plots/claim_frequency_province.png`, `claim_severity_postcode.png`, `margin_gender.png`
- `task-3` branch

