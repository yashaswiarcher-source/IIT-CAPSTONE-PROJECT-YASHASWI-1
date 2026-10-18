# Mamaearth — Part 2: Python/Pandas Data Wrangling & EDA

This is a standalone submission for Part 2. It runs directly against the three raw CSV files in `data/` and does not require the SQL database. Part 1 SQL and Part 2 Python can therefore be run in either order.

## Run from the repository root

```bash
python -m pip install -r requirements.txt
python analysis/clean_and_eda.py
python analysis/visualize.py
```

The scripts print the intermediate results referenced by the task brief and regenerate the two PNG files under `visualizations/`.

## Task coverage

- **Task 1:** Loads all three CSV files with `pd.read_csv`; raw orders shape is `(180, 9)`.
- **Task 2:** Applies `.str.strip().str.upper()` to payment methods; final counts are CARD 70, UPI 55, COD 55.
- **Task 3:** Detects duplicates using the required natural key, removes exactly five rows, and prints `O0176` through `O0180`; cleaned shape is `(175, 9)`.
- **Task 4:** Fills 12 missing discounts with `0`, fills 15 missing ratings with the deduplicated-frame median `3.0`, and prints the remaining null counts.
- **Task 5:** Merges products and customers, computes discounted `order_value`, prints the cleaned total `₹97,358.30`, and prints the reconciliation note explaining the exact `₹2,501.90` duplicate delta from Part 1’s `₹99,860.20` raw total.
- **Task 6:** Computes the IQR bounds `-0.5` to `3.5` and flags, without dropping, `O0011` quantity 25 and `O0098` quantity 30.
- **Task 7:** Prints the COD hypothesis and verifies return rates of CARD 14.7%, COD 44.4%, and UPI 18.9%; the hypothesis is labeled **Confirmed**.
- **Task 8:** Performs payment-method and city-tier segmentation and identifies COD + Tier-2 as the highest-risk segment at 54.5%, versus Tier-1 COD at 37.5%.
- **Task 9:** Prints the correlation matrix, labels all six pairs as negligible, and labels the higher-discount/lower-return hypothesis **Busted**.
- **Task 10:** Prints monthly order value including and excluding outliers and identifies March 2026 at `₹20,318.90` as the genuine peak after excluding the two January bulk-order outliers.
- **Task 11:** `visualize.py` regenerates `return_rate_by_payment.png` and `monthly_revenue_trend.png` with the required labels and titles.

## Files

```text
analysis/clean_and_eda.py
analysis/visualize.py
data/orders.csv
data/customers.csv
data/products.csv
visualizations/return_rate_by_payment.png
visualizations/monthly_revenue_trend.png
requirements.txt
README.md
```

The CSV source files are kept unchanged. The SQL database is intentionally not included because this Part 2 brief explicitly requires an independent pandas pipeline over the raw CSV files.
