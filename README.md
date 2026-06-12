# Financial Health Forecasting — Pakistan Stock Exchange

> Predicting next-year financial ratios and composite health scores for 70 PSX-listed companies across 15 sectors using machine learning and macroeconomic integration.

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazonaws&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-189A38?style=flat&logo=xgboost&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=flat&logo=sqlite&logoColor=white)

---

## The Problem

Financial analysis in Pakistan's corporate sector is almost entirely backward-looking — analysts compute historical ratios and leave investors to extrapolate. This is especially limiting in Pakistan's volatile macroeconomic environment (exchange rate shocks, inflation cycles, policy rate swings), where last year's numbers can be poor predictors of next year's reality.

This project builds a forward-looking system: upload a company's audited financial statements, and get a predicted set of 30 financial ratios for the next year, plus a composite Financial Health Score (0–100) for cross-company and cross-sector comparison.

---

## System Architecture

```
Excel Financial Statements
         │
         ▼
Flask Web Portal (AWS EC2)
  ├── Authentication
  ├── File upload & validation
  └── Ratio computation (34 ratios)
         │
         ▼
SQLite Database (18 years, 2005–2022)
  └── 70 companies × 15 sectors
         │
         ▼
Feature Engineering (113 features)
  ├── Lag features (lag1, lag2)
  ├── 3-year rolling averages
  ├── Year-over-year growth rates
  ├── Trend direction indicators
  ├── Normalized financial ratios
  └── Macroeconomic variables (×4)
         │
         ▼
ML Prediction Engine
  └── Best-per-ratio model selection
      from 7 algorithms
         │
         ▼
Financial Health Score (0–100)
  └── 14 weighted ratios →
      company ranking + sector benchmarks
```

---

## Web Portal (AWS)

The system runs on AWS EC2 with a Flask-based web interface. Authorized users can:

| Feature | Description |
|---|---|
| Upload financial data | `.xlsx` statements → auto-processed into SQLite |
| View company data | Browse 18 years of financial line items per company |
| View calculated ratios | 30 ratios auto-computed and displayed year-by-year |
| Manage ratios | Add, edit, or delete ratio definitions — changes apply to all companies |
| API access | Generate API keys to retrieve data programmatically |
| Data summary | Dashboard of all companies, data coverage, and record counts |

> The portal validates, cleanses, restructures, and stores uploaded statements automatically — no manual data entry required.

---

## Dataset

| Attribute | Detail |
|---|---|
| Companies | 70 PSX-listed companies |
| Sectors | 15 (Sugar, Textile, Cement, Oil/Gas, Pharma, FMCG, Tech, Auto, and more) |
| Time period | 2005–2022 (18 years) |
| Training rows | 408 (years 2006–2019) |
| Test rows | 205 (years 2020–2022) |
| Target variables | 30 financial ratios |
| Total features | 113 (reduced to 60 after feature selection) |

### Companies by sector

| Sector | Companies |
|---|---|
| Sugar | AL NOOR SUGAR MILLS, BABA FARID SUGAR MILLS, Chashma Sugar, Jauharabad Sugar, SHAHTAJ LIMITED, Tandlianwala Sugar, Mehran Sugar |
| Textile | Nishat Mills, MASOOD TEXTILE, Nishat (Chun.), DIN TEXTILE, GADOON TEXTILE, Blessed Textile, Elahi Cotton |
| Cement | LUCKY CEMENT, bestway cement, GHARIBWAL CEMENT, POWER CEMENT, THATTA CEMENT, Cherat Cement, Safe Mix, DADABHOY CEMENT |
| Oil & Gas | MARI PETROLEUM, Oil & Gas Dev., Hub Power, PSO, Shell Pakistan, Attock Refinery, KAPCO, Ghani Gases |
| Pharma/Chemical | ITTEHAD CHEM, BERGER PAINTS, Highnoon, Otsuka Pak, Nimir Ltd., Sitara Peroxide, Leiner Pak Gelatine, Ferozsons, Bawany Air |
| Engg/Steel | PAKISTAN CABLES, INTERNATIONAL INDUSTRIES, BOLAN CASTING, AISHA STEEL, INTER STEEL, Exide Pakistan, EMCO, Gammon Pak |
| FMCG | COLGATE PALMOLIVE, RAFHAN MAIZE, Murree Brewery, Shield Corp, KHYBER TOBACCO, BATA PAKISTAN, Gillette Pakistan, SERVICE IND. |
| Engineering | DIAMOND INDUSTRIES, PAK Electron, Thal Industries |
| Auto | Hino Motors, PAK SUZUKI, Ghandhara Nissan |
| Technology | NETSOL technologies, Pak Datacom, P.T.C.L |
| Paper | Century Paper & Board Mill, FEROZE 1888 |
| Healthcare | SHIFA HOSPITAL, IBL Healthcare |
| Logistics | Pakistan International Container, PAK INTERNATIONAL BULK TERMINAL |
| Glass | TARIQ GLASS INDUSTRIES |
| Other | Engro Corp |

---

## Financial Ratios (30 Targets)

### Profitability (8)
Gross Profit Ratio · Operating Profit Ratio · EBT/Sales · EAT/Sales · Net Profit Ratio · Return on Equity · Return on Assets · Return on Capital Employed

### Liquidity (3)
Current Ratio · Acid Test Ratio · Cash Ratio

### Leverage / Solvency (4)
Debt to Equity Ratio · Debt to Total Assets · Debt to Capital · Interest Coverage

### Efficiency / Turnover (8)
Inventory Turnover Ratio · Account Payable Turnover · Account Receivable Turnover Ratio · Asset Turnover Ratio · Fixed Asset Turnover · Avg Collection Period · Avg Payment Days · Operating Cycle

### Cash & Working Capital (1)
Cash Conversion Cycle

### Market / Investor (6)
P/E Ratio · Price to Book Value · Price to Sales · Book Value per Share · Sales per Share · Days Inventory Outstanding

---

## Macroeconomic Features

Four external variables integrated as features for all companies and years:

| Variable | Source |
|---|---|
| PKR/USD exchange rate | State Bank of Pakistan |
| CPI inflation rate (%) | Pakistan Bureau of Statistics |
| SBP monetary policy rate (%) | State Bank of Pakistan |
| Brent crude oil price (USD/bbl) | International Energy Agency |

---

## Feature Engineering

Starting from 30 raw ratios, 113 features were engineered per company-year:

| Feature type | Count | Description |
|---|---|---|
| Lag features (lag1, lag2) | 60 | Prior 1- and 2-year ratio values |
| 3-year rolling averages | 30 | Smoothed trend per ratio |
| Trend direction | 6 | YoY change for 6 key ratios |
| Normalized financial ratios | 10 | Scale-free versions (debt/assets, CFO margin, etc.) |
| Macroeconomic variables | 4 | PKR/USD, CPI, policy rate, oil |
| Interaction features | 2 | Cross-variable products |
| Year | 1 | Temporal signal |

Features with >40% missing values were dropped. Gap-aware logic prevents lag features from bridging the 2015→2018 reporting gap (i.e., 2018 does not use 2015 as lag1).

Final feature count after selection: **60 features**.

---

## Models

Seven algorithms were trained and evaluated per ratio. The best-performing model per ratio was selected independently.

| Model | Ratios won | Avg R² |
|---|---|---|
| **Lasso** | **11 / 30** | competitive |
| XGBoost | 8 / 30 | **0.655** (highest avg) |
| Gradient Boosting | 5 / 30 | — |
| ElasticNet | 3 / 30 | — |
| Random Forest | 2 / 30 | — |
| Extra Trees | 1 / 30 | — |
| KNN | 0 / 30 | — |

> Lasso won the most ratios despite its simplicity, demonstrating that many financial ratios are governed by approximately linear temporal dynamics. XGBoost led on average R², excelling at non-linear patterns in leverage and efficiency ratios.

**Validation:** 5-fold time-series cross-validation with a 1-year forward gap. Training always precedes test in time — no look-ahead bias.

---

## Results

### Per-ratio prediction performance (test set)

| Ratio | Best Model | R² | RMSE | MAE |
|---|---|---|---|---|
| Return on Equity | Lasso | **1.00** | 1.10 | 0.78 |
| Net Profit Ratio | Lasso | 0.99 | 1.27 | 0.81 |
| EAT/Sales | Lasso | 0.98 | 1.90 | 1.23 |
| Gross Profit Ratio | Lasso | 0.96 | 3.39 | 2.15 |
| EBT/Sales | Lasso | 0.94 | 3.86 | 2.28 |
| Interest Coverage | Lasso | 0.94 | 7.61 | 5.40 |
| Debt to Total Assets | XGBoost | 0.93 | 0.05 | 0.03 |
| Operating Profit Ratio | Lasso | 0.92 | 4.60 | 2.54 |
| Debt to Capital | XGBoost | 0.91 | 0.06 | 0.04 |
| Inventory Turnover | Lasso | 0.91 | 1.27 | 0.82 |
| Return on Capital Employed | Lasso | 0.91 | 6.26 | 4.27 |
| Return on Assets | Lasso | 0.90 | 2.74 | 1.89 |
| Account Payable Turnover | Lasso | 0.90 | 1.43 | 1.02 |
| Debt to Equity | XGBoost | 0.88 | 0.24 | 0.14 |
| Cash Ratio | Gradient Boosting | 0.88 | 0.15 | 0.09 |
| Acid Test Ratio | Gradient Boosting | 0.83 | 0.31 | 0.19 |
| Current Ratio | Gradient Boosting | 0.83 | 0.40 | 0.24 |
| Avg Collection Period | ElasticNet | 0.82 | 31.47 | 19.37 |
| Avg Payment Days | Gradient Boosting | 0.79 | 64.89 | 36.04 |
| Asset Turnover Ratio | XGBoost | 0.75 | 0.33 | 0.24 |
| Account Receivable Turnover | XGBoost | 0.69 | 24.93 | 12.19 |
| Operating Cycle | ElasticNet | 0.46 | 111.96 | 66.09 |
| Cash Conversion Cycle | XGBoost | 0.44 | 104.64 | 63.93 |
| Fixed Asset Turnover | XGBoost | 0.43 | 3.73 | 2.08 |
| Days Inventory Outstanding | Random Forest | 0.42 | 54.56 | 26.98 |
| Price to Book Value | XGBoost | 0.18 | 4.80 | 3.56 |
| Sales per Share | Extra Trees | 0.11 | 442.25 | 305.25 |
| Price to Sales | Extra Trees | 0.09 | 3.10 | 2.12 |
| Book Value per Share | Extra Trees | 0.03 | 168.99 | 118.29 |
| P/E Ratio | Extra Trees | 0.02 | 38.91 | 26.54 |

### Summary

| Metric | Value |
|---|---|
| Ratios with R² > 0.60 | **21 / 30 (70%)** |
| Ratios with R² > 0.80 | 17 / 30 |
| Median R² across all ratios | **0.857** |
| Mean R² across all ratios | **0.695** |

> Market-based ratios (P/E, P/B, Price-to-Sales) were the hardest to predict from accounting fundamentals alone — investor sentiment dynamics are not captured in financial statements, which motivates future NLP sentiment integration.

---

## Financial Health Score

A composite 0–100 score built from **14 weighted ratios** with R² > 0.60, covering four dimensions:

| Dimension | Weight | Ratios included |
|---|---|---|
| Profitability | 35% | Gross Profit, Net Profit, ROE, ROA, ROCE |
| Liquidity | 25% | Current Ratio, Acid Test, Cash Ratio |
| Solvency | 20% | Debt-to-Equity, Interest Coverage, Debt-to-Capital |
| Efficiency | 20% | Asset Turnover, Inventory Turnover, Payable Turnover |

### 2023 Predicted Rankings (top companies)

| Rank | Company | Sector | Score | Grade |
|---|---|---|---|---|
| 1 | MARI PETROLEUM | Oil & Gas | 77.3 | A — Excellent |
| 2 | RAFHAN MAIZE | FMCG | 74.1 | A — Excellent |
| 3 | COLGATE PALMOLIVE | FMCG | 72.8 | A — Excellent |
| ... | ... | ... | ... | ... |

### Grade distribution (2023 predictions)

| Grade | Threshold | Companies | % |
|---|---|---|---|
| A — Excellent | Score ≥ 70 | ~5 | ~7% |
| B — Good | Score 55–70 | ~14 | ~20% |
| C — Average | Score 40–55 | ~48 | 69% |
| D — Weak | Score 25–40 | ~3 | ~4% |

> 69% of companies landing in Grade C reflects the historically adverse 2022 macroeconomic conditions: simultaneous record-high inflation, PKR depreciation, and elevated SBP policy rates.

### Sector rankings (predicted 2023 health)

**Leading sectors:** Oil & Gas · Technology · Pharmaceuticals/Chemical · FMCG

**Weakest sectors:** Sugar · Engineering/Steel

---

## Visualizations

The notebook produces 8 publication-quality charts:

| Chart | Description |
|---|---|
| `viz1_health_leaderboard.png` | Top 20 + bottom 5 companies by health score |
| `viz2_sector_comparison.png` | Box + jitter plot of health scores by sector |
| `viz3_model_r2_heatmap.png` | R² heatmap: 7 models × 30 ratios |
| `viz4_model_comparison.png` | Model win count + average R² (dual panel) |
| `viz5_cv_folds.png` | Time-series CV fold-by-fold R² per model |
| `viz6_ratio_performance.png` | R², RMSE, MAE bar chart for all 30 ratios |
| `viz7_macro_trends.png` | PKR/USD, Inflation, Policy Rate, Oil — 2005–2022 |
| `viz8_grade_dist_sector_heatmap.png` | Grade distribution pie + sector health heatmap |

---

## Repository Structure

```
financial-health-forecasting-psx/
│
├── FYP_Final.ipynb              # Main notebook — full pipeline
├── per_ratio_models.pkl         # Trained models bundle (pickle)
├── ratio_performance.csv        # Per-ratio R², RMSE, MAE results
│
├── data/
│   └── macro_data.xlsx          # Macroeconomic variables (2005–2022)
│
├── outputs/
│   ├── viz1_health_leaderboard.png
│   ├── viz2_sector_comparison.png
│   ├── viz3_model_r2_heatmap.png
│   ├── viz4_model_comparison.png
│   ├── viz5_cv_folds.png
│   ├── viz6_ratio_performance.png
│   ├── viz7_macro_trends.png
│   └── viz8_grade_dist_sector_heatmap.png
│
├── portal_screenshots/
│   ├── login_page.png
│   ├── home_dashboard.png
│   ├── financial_data_view.png
│   └── ratio_data_view.png
│
├── requirements.txt
└── README.md
```

---

## Setup & Usage

### Prerequisites

```bash
pip install -r requirements.txt
```

**requirements.txt**
```
pandas
numpy
scikit-learn
xgboost
matplotlib
seaborn
requests
pickle5
```

### Loading the trained models

```python
import pickle
import numpy as np

with open("per_ratio_models.pkl", "rb") as f:
    bundle = pickle.load(f)

best_models  = bundle["best_models"]
scaler       = bundle["scaler"]
feature_cols = bundle["feature_cols"]
ratio_cols   = bundle["ratio_cols"]

# Predict for a new company-year row:
X_new = scaler.transform(new_row[feature_cols].fillna(0))
predictions = {}
for col in ratio_cols:
    info = best_models[col]
    pred = info["model"].predict(X_new)[0]
    predictions[col] = float(pred)
```

### Running the full pipeline

Open `FYP_Final.ipynb` and run cells in order:

1. **Cell 1** — Imports & company list
2. **Cell 2** — API configuration (requires AWS portal access)
3. **Cell 3** — Helper functions
4. **Cell 4** — Fetch data from API
5. **Cell 5** — Data cleaning & transformation
6. **Cell 6** — Feature engineering (produces 113 features)
7. **Cell 7** — Feature selection & train/test split
8. **Cell 8** — RobustScaler normalization
9. **Cell 9** — Model training, CV, and evaluation
10. **Cell 10** — Predict 2023 ratios for all companies
11. **Cell 11** — Save model bundle + performance report
12. **Cell 12** — Financial Health Score computation
13. **Cells 13–21** — Visualizations (8 charts)

> Note: Cells 2–4 require a live API key and access to the AWS portal. All other cells can be run using the saved `per_ratio_models.pkl` bundle.

---

## Key Design Decisions

**Best-per-ratio model selection** — rather than applying one model to all 30 ratios, the best algorithm is selected independently per ratio. This gave 10–12% better performance than using a single model across the board.

**Time-series cross-validation with a 1-year forward gap** — standard k-fold would leak future information. A 5-fold time-series split with a 1-year gap between train and validation ensures honest out-of-sample evaluation.

**Gap-aware lag features** — the PSX data has a reporting gap (2015–2018) for many companies. Lag features are nullified across this gap to prevent 2018 from incorrectly using 2015 as its "prior year."

**RobustScaler (fit on train only)** — scaler is fitted on training data and applied to test data. Prevents test set statistics from leaking into training normalization.

**Market ratios are predictably hard** — P/E, P/B, and Price-to-Sales achieved R² < 0.20. This is expected: market prices reflect investor sentiment, which is not captured in historical accounting ratios. NLP-based sentiment analysis is the natural next step.

---

## Limitations

- Market-based ratios (P/E, P/B, Price-to-Sales) have low R² — accounting features alone cannot capture investor sentiment
- Small sample size per company (~15 years) limits deep learning approaches
- 2022 macroeconomic shock (record inflation + PKR depreciation) makes 2023 predictions noisier than typical years
- Coverage limited to 70 companies across 15 sectors — not representative of the full PSX universe

---

## Future Work

- NLP sentiment analysis from financial news and annual report text (MD&A sections)
- LLM integration for interactive querying of financial data and report summarization
- Real-time sector dashboards with live ratio updates
- Deep learning multi-step forecasting (LSTM, Transformer-based)
- Expanded macroeconomic coverage (FDI flows, trade balance, sector-specific indices)
- Broader company coverage across all PSX-listed firms

---

## Tech Stack

| Layer | Technology |
|---|---|
| Data pipeline | Flask, AWS EC2, SQLite |
| Data processing | Python, Pandas, NumPy |
| Machine learning | scikit-learn, XGBoost |
| Feature engineering | Custom time-series transformations |
| Visualization | Matplotlib, Seaborn |
| Model persistence | pickle |

---

## About

Final Year Project (FYP) — BS Business Analytics  
Pakistan Stock Exchange financial health forecasting using machine learning  
18 years of data · 70 companies · 15 sectors · 30 financial ratios · 7 ML models
