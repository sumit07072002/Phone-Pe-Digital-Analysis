# PhonePe Pulse — Digital Payments Transaction & User Analytics

A Python-based exploratory data analysis (EDA) project studying PhonePe Pulse data across Indian states and districts from **Q1 2018 to Q2 2021**. The notebook explores quarterly transaction trends, transaction categories, registered users, device-brand distribution, district demographics, and consistency between state- and district-level records.

> **Data note:** The notebook references an Excel workbook named `phonepe-pulse_raw-data_q12018-to-q22021-v0-1-5-1720351752.xlsx`. That workbook was not included with the uploaded notebook. Add the source workbook locally before rerunning the complete analysis, and update the file path in the data-loading cell if needed.

## Project Objectives

- Measure how transaction counts and transaction value changed quarter by quarter.
- Compare states with the highest and lowest cumulative transaction counts in the observed period.
- Explore differences in transaction categories across states and quarters.
- Examine the recorded distribution of registered users by mobile-device brand.
- Identify the most populous district within each state using the demographic table.
- Reconcile state-level totals with sums of district-level records.
- Explore the association between district population and recorded transaction volume.
- Identify missing values and calculation or visualization issues that should be addressed before business decisions are made.

## Tools & Libraries

- **Python** — analysis workflow
- **pandas** — data loading, grouping, merging, pivoting, and quality checks
- **NumPy** — numerical operations
- **Matplotlib** — charting
- **Seaborn** — statistical visualizations
- **Jupyter Notebook / Google Colab** — interactive analysis environment

## Data Tables Used

The notebook loads six sheets from the source Excel workbook:

| Table / sheet | Rows shown in notebook | Main use |
|---|---:|---|
| `State_Txn and Users` | 504 | State-quarter transaction count, transaction amount, ATV, registered users, and app opens |
| `State_TxnSplit` | 2,514 | Transactions and amount by transaction category, state, year, and quarter |
| `State_DeviceData` | 5,544 | Recorded user counts and percentages by device brand and state-quarter |
| `District_Txn and Users` | 10,248 | District-quarter transactions, amount, ATV, registered users, and app opens |
| `District Demographics` | 742 | District population, area, density, headquarters, and codes |
| `Admin` | Loaded by notebook | Administrative/reference data; detailed use should be confirmed from the workbook |

The transaction tables cover **14 quarters**, from Q1 2018 through Q2 2021. Row counts above are taken from the notebook's saved outputs, not from a fresh run against the original workbook.

## Analysis Workflow

1. **Load and inspect data** with `head()`, `info()`, `describe()`, and null-value checks.
2. **Explore state performance** by grouping transaction counts and amounts, then ranking states by cumulative transaction count.
3. **Review transaction categories** by state and quarter and visualize the category distribution for a selected period.
4. **Analyze device brands** by joining device records to state-level registered-user totals and calculating each brand's share of the recorded total.
5. **Explore district demographics** by identifying the highest-population district in each state.
6. **Create a district-code mapping** and export unique district/code pairs to CSV.
7. **Check data consistency** by aggregating district-level transactions, amounts, and registered users by state-quarter and comparing them against state-level values.
8. **Visualize trends** for the overall data and for Uttar Pradesh.
9. **Explore population association** by calculating a Pearson correlation between district population and recorded transaction count.

## Key Findings From the Notebook's Saved Outputs

### 1. Overall quarterly growth

The notebook's quarterly totals, calculated from the transaction-category table, show a strong increase over the observed period:

- Total transaction count increased from approximately **134.4 million in Q1 2018** to **3.94 billion in Q2 2021** — around **29.3 times** the initial level.
- Total transaction amount increased from approximately **INR 0.172 lakh crore in Q1 2018** to **INR 7.48 lakh crore in Q2 2021** — around **43.5 times** the initial level.
- Growth was not monotonic. From Q1 2020 to Q2 2020, recorded transaction count decreased by approximately **10.7%**, while transaction amount decreased by approximately **1.9%**. Both measures rose in later quarters in the saved series.

These are descriptive changes in the dataset; the notebook does not establish the cause of the Q2 2020 decline or subsequent growth.

### 2. Highest and lowest cumulative state transaction counts

Based on the notebook's state-level transaction table, the five highest cumulative transaction counts are:

1. Karnataka — approximately 2.98 billion
2. Maharashtra — approximately 2.83 billion
3. Telangana — approximately 2.35 billion
4. Andhra Pradesh — approximately 1.78 billion
5. Rajasthan — approximately 1.38 billion

The five lowest are Lakshadweep, Andaman & Nicobar Islands, Ladakh, Mizoram, and Meghalaya.

These rankings reflect **total recorded activity across the period**, not transaction activity per resident or per registered user. Differences in population, market size, and data coverage should be considered before interpreting state performance.

### 3. Uttar Pradesh trend

In the Uttar Pradesh transaction-category series shown in the notebook:

- Quarterly transaction count rose from approximately **12.5 million in Q1 2018** to **241.9 million in Q2 2021** (about **19.3 times** the initial level).
- Quarterly transaction amount rose from approximately **INR 13.94 billion** to **INR 482.17 billion** (about **34.6 times** the initial level).

The notebook also charts transaction-category distribution for Uttar Pradesh in Q2 2021.

### 4. District population and transaction activity

The saved correlation between district population and the district-quarter transaction count is approximately **0.359**, indicating a modest positive linear association in the analysed records.

This is an exploratory result, **not evidence that population causes higher transaction volume**. Population is repeated across quarters in the analysis, and the result is not population-normalized. A district-level per-capita or per-registered-user metric would provide a fairer comparison.

### 5. Data-quality checks

The notebook's null checks report:

- One missing `Amount (INR)` value in the state transactions/users table.
- 28 missing district codes and four missing `ATV (INR)` values in the district transactions/users table.
- No null values reported in the transaction-split, device-brand, or demographic tables.

The state-versus-district reconciliation flags two records because their amount totals differ by about **INR 0.01** due to small floating-point or rounding differences. The transaction counts and registered-user totals match for those rows. This suggests tolerance-based comparison is preferable for monetary totals.

## Important Limitations and Recommended Corrections

This notebook is an exploratory analysis, and several calculations should be corrected or validated before the results are used for business decisions:

- `txn_users['Transactions'].count()` counts non-null rows, not the total number of transactions. Use `.sum()` for transaction volume.
- The transaction-category pivot uses `aggfunc='count'`, which counts records. To identify the most common category by transaction volume, aggregate the `Transactions` field with `.sum()`.
- The Uttar Pradesh demographic chart is titled as population density, but its y-axis uses `Population`. Use `Density` if the intended metric is population density, or relabel the chart as population.
- The state ATV comparison uses an unweighted mean of category-level ATV values. For a combined average ticket value, calculate total transaction amount divided by total transaction count at the same aggregation level, provided both measures are defined consistently.
- Device-brand counts are quarterly records. Summing them over time should not be described as unique people unless the source methodology explicitly supports that interpretation.
- Validate zero app-open values and zero population/area values before treating them as real observations. Missing, unavailable, and true-zero values should not automatically be treated as equivalent.
- The correlation calculation uses population with quarter-level transaction records and does not calculate population density or per-capita activity. Consider aggregating to one record per district or explicitly modelling the repeated-quarter structure.
- The source workbook should be included or cited in the project repository with appropriate usage and attribution information before the project is shared publicly.

## How to Run

1. Clone or download this repository.
2. Install the required libraries:

   ```bash
   pip install pandas numpy matplotlib seaborn openpyxl jupyter
   ```

3. Place the source workbook in the project folder, or update the path in the notebook's loading cell. The original notebook currently uses a Google Colab-style path under `/content/`.
4. Open `Phone_Pe_case_study.ipynb` in Jupyter Notebook, JupyterLab, or Google Colab.
5. Run the cells in order and review the outputs. Re-run all cells after making changes to ensure displayed results are current.

## Repository Structure

A suggested layout for publishing the project is:

```text
phonepe-pulse-analysis/
├── README.md
├── Phone_Pe_case_study.ipynb
├── data/
│   └── phonepe-pulse-source-workbook.xlsx   # add locally; not included here
├── outputs/
│   └── unique_district_name_code_mapping.csv
└── images/
    └── charts/                             # optional exported charts
```

The folder layout is a recommendation. The current notebook exports `unique_district_name_code_mapping.csv` to its working directory; it does not automatically create the folders shown above.

## Business Recommendations

- Compare states using both total activity and normalized metrics such as transactions per registered user or per resident.
- Investigate quarter-on-quarter declines instead of assuming a cause from the trend alone.
- Monitor transaction count, transaction amount, and correctly calculated average ticket value together.
- Use category
