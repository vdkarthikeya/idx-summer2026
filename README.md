# IDX Exchange Summer 2026 Internship — California Property Close Price Prediction

Predicting the close (final sale) price of California single-family residential properties using historical CRMLS MLS data.

## Project Structure

```
idx-summer2026/
├── data/
│   ├── notebooks/
│   │   ├── 01_exploration.ipynb       # Week 2: EDA
│   │   └── 02_preprocessing.ipynb     # Week 3: Cleaning + train/test split
│   ├── raw_data/                       # Raw CRMLSSold CSVs (not tracked in git)
│   └── processed/                      # Cleaned CSVs (not tracked in git)
├── .gitignore
└── README.md
```

## Data Source

CRMLS (California Regional Multiple Listing Service) monthly sold-listing exports, accessed via FTP. 7 months of data used: November 2025 – May 2026.

Filtered to `PropertyType = Residential` and `PropertySubType = SingleFamilyResidence` per task requirements.

## Progress

### Week 1 — Setup ✅
- Downloaded 7 months of CRMLS data via FileZilla FTP
- Set up Python environment (VS Code + Jupyter)
- Initialized GitHub repo

### Week 2 — Exploration ✅
**Notebook:** `data/notebooks/01_exploration.ipynb`

- Loaded and concatenated 7 monthly CSVs → 143,492 rows × 79 columns
- Filtered to Residential/SingleFamilyResidence → 71,466 rows × 79 columns
- Confirmed `ClosePrice` has 0 nulls, 0 zeros/negatives at raw stage
- Identified right-skew in `ClosePrice`; decided to model `log1p(ClosePrice)`
- Ran full missingness audit across all 79 columns
- Identified core modeling features: `LivingArea`, `BedroomsTotal`, `BathroomsTotalInteger`, `LotSizeSquareFeet`, `YearBuilt`, `Latitude`, `Longitude`, `CloseDate`
- Flagged garbage values for Week 3 cleanup (zero values, extreme outliers in `LotSizeSquareFeet`, `YearBuilt` min of 1801)
- Exported filtered dataset to `data/processed/week2_filtered.csv`

**Key columns dropped (80%+ missing):** `CoveredSpaces`, `MiddleOrJuniorSchoolDistrict`, `TaxYear`, `ElementarySchoolDistrict`, `BusinessType`, `TaxAnnualAmount`, `FireplacesTotal`, `AboveGradeFinishedArea`, `WaterfrontYN`, `BelowGradeFinishedArea`, `BasementYN`, `BuilderName`

### Week 3 — Preprocessing ✅
**Notebook:** `data/notebooks/02_preprocessing.ipynb`

Cleaned the dataset feature-by-feature, checking both tails of each distribution rather than relying only on Week 2's initial flags:

| Step | Action | Rows affected |
|---|---|---|
| Column drop | Removed 12 dead/near-dead columns | 79 → 67 columns |
| Zero-value rows | Dropped rows with `LivingArea`, `BedroomsTotal`, or `BathroomsTotalInteger` = 0 | 92 rows |
| Coordinates | Dropped rows missing `Latitude`/`Longitude` | 9 rows |
| `LotSizeSquareFeet` outliers | Capped at 99.9th percentile (~6.97M sqft) instead of a fixed acreage cap, after discovering most "outlier" values were plausible large rural lots, not data errors | 68 rows |
| `LotSizeSquareFeet` missing | Imputed with median (right-skewed distribution) | ~1.7% of rows |
| `GarageSpaces` outliers | Capped at 10 (values up to 600 found — clear data entry errors) | 36 rows |
| `GarageSpaces` missing | Imputed with 0 (median of 2.0 rejected — would falsely assert a specific garage count; 0 is a common real value, ~8% of data) | ~3.8% of rows |
| `YearBuilt` invalid | Dropped rows below CA statehood (1850); found 3 rows all reading 1801, likely a placeholder value | 3 rows |
| `ClosePrice` low outliers | Dropped implausible sub-$10K sales (as low as $1.75) — not real market transactions | 4 rows |
| `ClosePrice` high outliers | Dropped sales above $200M — genuine CA luxury sales top out near $200M; 8 rows at $472M–$796M were data errors | 8 rows |
| Target transform | Applied `log1p(ClosePrice)` → `log_price` | — |
| Train/test split | Time-based: train = Nov 2025–Apr 2026, test = May 2026 (avoids leaking future data) | 59,316 train / 12,000 test |

**Final cleaned dataset:** 71,316 rows × 68 columns before split.

**Output files** (not tracked in git): `data/processed/train_clean.csv`, `data/processed/test_clean.csv`

### Week 4 — TODO
- Train baseline Linear Regression model
- Evaluate on test set using R²

## Setup

```bash
pip install pandas numpy matplotlib
```

Raw data files are excluded from version control (see `.gitignore`) — request FTP access separately per the task prompt.
