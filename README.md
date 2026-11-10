# FERC Form 552 Next-Day Trading Forecast

## Project objective
This project asks whether a natural-gas market participant’s transaction profile in year \(t\) can predict whether it will be a high-intensity fixed-price next-day trader in year \(t+1\).

A company is labeled **high-intensity** if its fixed-price next-day transaction share is at or above the 75th percentile for that year.

## Data
Source: FERC Form 552 Master Table, 2008–2025.

Raw filing rows are cleaned by:
- resolving superseded revisions,
- aggregating filings to reporting-company/year,
- dropping company-years with zero total physical volume,
- constructing transaction-type shares,
- matching companies across consecutive years.

The final modeling sample contains **9,172 consecutive-year prediction pairs**.

## Model and evaluation
The course-method baseline is **L2 logistic regression** using eight year-\(t\) predictors, including total volume, purchase share, prior next-day share, and transaction-type shares.

Evaluation is strictly chronological:

- Forward validation: target years 2014–2023
- Checkpoint evaluation: 2024
- Final untouched test: 2025

Regularization is selected by forward-validation average precision. The classification threshold is selected using validation years only.

Persistence—predicting next year’s class from the current year’s class—is the main benchmark.

## Checkpoint results
On the 2024 checkpoint sample (\(N=529\)):

| Model | Error rate |
|---|---:|
| Majority | 26.65% |
| Persistence | 8.70% |
| L2 Logistic | **8.51%** |

Logistic regression achieved:

- Average Precision: **0.876**
- AUC: **0.948**
- Logistic − persistence error difference: **−0.19 percentage points**
- 95% paired company-bootstrap CI: **[−1.89, 1.51] percentage points**

The logistic model ranks future high-intensity traders well, but its reduction in classification error relative to persistence is not statistically distinguishable from zero.

## Repository structure
```text
.
├── 01_final_checkpoint.ipynb
├── data_raw/
│   └── form552/
├── data_processed/
├── figures/
└── README.md
```

## Reproduction
1. Place the FERC Form 552 Master Table and data dictionary in `data_raw/form552/`.
2. Open `01_final_checkpoint.ipynb`.
3. Restart the kernel and run all cells from top to bottom.
4. Confirm that the 2025 test set remains untouched during checkpoint analysis.

Dependencies: `pandas`, `numpy`, `matplotlib`, `scikit-learn`.
