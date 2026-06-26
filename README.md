# IDX Exchange — California Property Price Prediction

## Project Overview
Building a machine learning model to predict close prices 
of single-family residential properties in California 
using CRMLS MLS data.

## Data
- Source: CRMLS via IDX Exchange FTP server
- 7 months of data: November 2025 - May 2026
- Filtered to: PropertyType=Residential, PropertySubType=SingleFamilyResidence
- Final dataset: 71,466 rows x 79 columns
- Raw data files are not included in this repo

## Progress
- [x] Week 1 - Setup & data access
- [x] Week 2 - Exploratory Data Analysis
- [ ] Week 3 - Preprocessing
- [ ] Week 4 - Baseline Model

## Notebooks
- `data/notebooks/01_exploration.ipynb` — EDA: distributions of ClosePrice, LivingArea, Bedrooms, Bathrooms, LotSize

## Key Findings (EDA)
- ClosePrice is right-skewed; will use log transform
- Several columns are 80-100% empty (will drop in preprocessing)
- Core features (LivingArea, Bedrooms, Bathrooms, LotSize, YearBuilt) have zero missing values
- 9 rows missing coordinates (will drop in preprocessing)

## How to Run
1. Clone the repo
2. Add your own data files to `data/raw_data/` (not included - contact IDX Exchange for access)
3. Run notebooks in order starting with `data/notebooks/01_exploration.ipynb`
EOF