# Financial Data Quality Audit (Excel + SQL)

An end-to-end data audit project: take a messy finance transactions dataset, find every quality issue with **Excel** and **SQL**, clean it, and report the results.

## Problem
A company's transaction records (515 rows) contain duplicates, missing values, wrong totals and inconsistent labels. Before delivering the data to a client, it must be audited and cleaned.

## Tools
- MS Excel (`COUNTIF`, `IF`, `ISNUMBER/MATCH`, `SUMIFS`, `SUMPRODUCT`, filters, freeze panes)
- SQL (SQLite-compatible): `GROUP BY/HAVING`, `CASE`, `DISTINCT`, NULL handling
- Python (pandas) only to generate the dataset and run the SQL

## Dataset
`data/raw_transactions.csv` is synthetic data (no real company information).
Columns: `txn_id, txn_date, customer_id, customer_name, department, category, amount, tax_pct, total_amount, payment_status`

## Data Quality Checks
| Check | Rule |
|---|---|
| Duplicates | `txn_id` appears more than once |
| Missing name / date | blank or NULL |
| Future dates | date after 29-Sep-2026 |
| Negative amount | `amount <= 0` |
| Total mismatch | `total_amount` != `amount * (1 + tax_pct/100)` |
| Department not standard | extra spaces / casing / unknown value |
| Invalid payment status | not Paid / Pending / Overdue |

## Results
| Issue | Count |
|---|---|
| Duplicate rows (all copies) | 30 (15 extra copies) |
| Total mismatch | 31 |
| Missing customer name | 13 |
| Missing date | 11 |
| Negative amount | 8 |
| Future-dated | 7 |
| Invalid payment status | 7 |
| Department not standard | 15 (Excel, ignoring case) / 28 (SQL, exact match) |

515 raw rows -> **457 clean rows** after cleaning.

## Cleaning Steps
1. Remove duplicate rows (`DISTINCT`)
2. Drop rows with missing name/date, future dates, non-positive amounts, invalid status
3. Standardise department names with `TRIM` + `UPPER` + `CASE`
4. Recalculate `total_amount` from `amount` and `tax_pct`

## Business Output
Revenue by department and outstanding (Pending/Overdue) amount per customer, in `sql/02_clean_and_report.sql` and the `Audit_Summary` sheet of the Excel workbook.

## Folder Structure
