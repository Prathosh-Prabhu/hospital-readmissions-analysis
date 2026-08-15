# US Hospital Readmission Analysis: Quality, Penalties, and Healthcare Inequality (2021-2024)

## Overview
This project analyzes hospital readmission rates across 2,428 US hospitals using official CMS (Centers for Medicare and Medicaid Services) data. A hospital readmission occurs when a patient is discharged and returns within 30 days — high readmission rates indicate poor care quality and result in direct financial penalties from the US government. This analysis investigates what drives readmission rates, which hospitals and states are most penalized, and where healthcare inequality is measurable in the data.

## Tools Used
- **SQL (SQLite)**: Data cleaning, joining three CMS datasets, exploratory queries
- **Python (Jupyter Notebook)**: Exploratory analysis, OLS regression, Random Forest, K Means clustering, SHAP values, penalty estimation
- **Pandas, NumPy, Matplotlib, Statsmodels, Scikit-learn, SHAP**: Core analysis libraries
- **Tableau**:  Interactive dashboard

## Data Sources
All data sourced directly from the CMS Provider Data Catalog (data.cms.gov):
- **FY2026 Hospital Readmissions Reduction Program**: readmission rates by hospital and condition
- **Hospital General Information**: hospital characteristics including ownership, star rating, location
- **Complications and Deaths**: additional quality metrics

Performance period: July 2021 to June 2024 (3-year rolling window)
FY2026 penalties applied based on this performance window

After cleaning and joining: 7,968 records across 2,428 hospitals in 51 states

## Data Cleaning (SQL)
- Joined three CMS datasets on Facility ID using SQLite
- Removed records with N/A values for key metrics
- Filtered to hospitals with available star ratings
- Cast numeric columns from text to appropriate data types
- Exported clean joined dataset for Python analysis

## Key Findings

### 1. 58% of US Hospitals Are Being Financially Penalized
1,412 of 2,428 hospitals (58%) have excess readmission ratios above 1.0, meaning they perform worse than the national benchmark. CMS reduces Medicare payments by up to 3% for these hospitals. Total estimated national penalties: $459 million annually.

### 2. Florida Bears the Highest Penalty Burden
Florida hospitals face an estimated $50.4 million in total Medicare penalties, the highest of any state. Massachusetts hospitals face the highest penalty per hospital ($619,000 average) despite the state's reputation for healthcare quality. Even 5-star rated hospitals are not immune, Northwestern Medicine McHenry faces an estimated $1.4 million penalty.

### 3. Healthcare Inequality Is Measurable in Readmission Data
Brooklyn hospitals are penalized at an 88% rate compared to 34% for Manhattan hospitals. Brooklyn's average star rating is 1.6 vs Manhattan's 4.17. The West is the only US region performing better than the national average (0.998), while the Southeast and Northeast consistently underperform.

### 4. Massachusetts Reputation vs Reality
Massachusetts performs worst among all states for 4 of 6 conditions tracked. However this is driven entirely by suburban community hospitals, Massachusetts General Hospital (0.919) and Brigham and Women's (0.981) actually perform well. The state average masks significant internal variation between world class Boston hospitals and struggling community hospitals.

### 5. Hip/Knee Replacement Is Volume-Sensitive
Small hospitals performing Hip/Knee replacement surgery have an average excess ratio of 1.41 while very large hospitals achieve 0.749, a 70 percentage point gap for the same procedure. This strongly suggests orthopedic surgery outcomes improve dramatically with hospital volume and specialization.

### 6. Regional Variation Is Condition-Specific
The Southeast performs worst for Hip/Knee replacement (1.093) and Heart Bypass (1.048) but the pattern shifts for Heart Failure where regional differences narrow. Hospital quality problems are not uniform across all care types — regions have specific weaknesses in specific conditions.

## Machine Learning Analysis

### OLS Regression
- R-squared: 0.118
- Each additional star rating reduces excess readmission ratio by 2.1% (p < 0.001)
- Larger hospitals perform slightly better (p < 0.001)

### Random Forest Regressor (hospital characteristics only)
- R-squared: 0.188 (significantly better than OLS, captures non-linear relationships)
- Star rating (35.6%) and number of discharges (33.2%) are top predictors of readmission ratio

### Classification Model (penalized vs not penalized)
- Accuracy: 65%
- ROC-AUC: 0.73
- Hospital size is strongest predictor of penalty likelihood

### K-Means Clustering (4 hospital archetypes)
- Cluster 1: High-rated good performers (avg ratio 0.978, avg rating 3.9), largest group, 945 hospitals
- Cluster 2: Low-rated poor performers (avg ratio 1.042, avg rating 2.3), 921 hospitals
- Cluster 3: Large hospitals (avg 2,854 discharges) performing slightly better than average
- Cluster 4: Mid-rated average performers

### Penalty Prediction Model
- R-squared: 0.957, highly accurate
- Hospital size (78% importance) dominates, a large hospital with a slightly high ratio faces far larger penalties than a small hospital with a terrible ratio

### SHAP Values
- Star rating and discharge volume each shift predicted excess ratio by approximately 0.017 on average
- Condition type has a small but real effect (0.002)
- State and ownership type are negligible at the individual prediction level

## Limitations
- Penalty estimates assume ~$13,000 average Medicare payment per discharge, actual payments vary significantly by procedure and patient complexity
- Data covers July 2021 to June 2024, may not reflect most recent hospital performance changes
- Analysis is at the hospital level, patient-level factors (age, comorbidities, insurance type) are not available in this dataset
- Geographic inequality findings may partially reflect patient population differences rather than care quality differences

## Interactive Dashboard
[US Hospital Readmission Analysis — Tableau Public](https://public.tableau.com/app/profile/prathosh.selluka/viz/USHospitalReadmissionAnalysis_17866659934390/Dashboard1?publish=yes)

## Project Structure
```
hospital-readmissions/
├── hospital_readmissions_analysis.ipynb   # Main analysis notebook
├── clean_hospital_data.csv                # Cleaned joined dataset from SQL
├── tableau_state_summary.csv             # State level summary for map
├── tableau_star_summary.csv              # Star rating analysis
├── tableau_top_penalized.csv             # Top 20 penalized hospitals
├── tableau_region_condition.csv          # Regional condition heatmap data
├── tableau_size_analysis.csv             # Hospital size analysis
├── tableau_nyc_hospitals.csv             # NYC borough comparison
├── tableau_clusters.csv                  # K-Means cluster assignments
├── tableau_size_condition.csv            # Condition by hospital size
└── README.md
```
