# Superstore Loss and Discount Analysis (SQL Server)

A beginner-level SQL Server project that loads the Sample - Superstore retail dataset, audits it, and investigates loss-making orders and the role of discounts.

**Business question:** Which orders lose the most money, and does heavy discounting explain it?

**Business Requirements Document:** The BRD for this project is complete. See [Analysis/Superstore_BRD.docx](Analysis/Superstore_BRD.docx) for the problem statement, business impact, objectives, scope, requirements, implementation plan and acceptance criteria.

## 1. Dataset

- **Name:** Sample - Superstore
- **Source:** Kaggle ([add the dataset link here])
- **Size:** 9,994 rows and 21 columns, one row per order line
- **Note:** This is a public sample dataset, not real company data, used for educational purposes. The CSV is included in the [Analysis](Analysis/) folder for convenience; the original source is the Kaggle link above.

### Data dictionary

| Column | SQL type | Description |
| --- | --- | --- |
| Row ID | INT (primary key) | Unique number for each row |
| Order ID | VARCHAR(20) | Order identifier; one order can have several rows |
| Order Date | VARCHAR(12) | Date the order was placed, stored as text |
| Ship Date | VARCHAR(12) | Date the order was shipped, stored as text |
| Ship Mode | VARCHAR(20) | Shipping method (4 values) |
| Customer ID | VARCHAR(20) | Customer identifier |
| Customer Name | VARCHAR(50) | Customer's name |
| Segment | VARCHAR(20) | Customer segment (Consumer, Corporate, Home Office) |
| Country | VARCHAR(30) | Country of the order |
| City | VARCHAR(50) | City of the order |
| State | VARCHAR(50) | State of the order |
| Postal Code | VARCHAR(10) | Postal code, stored as text because it is an identifier |
| Region | VARCHAR(10) | Sales region (Central, East, South, West) |
| Product ID | VARCHAR(20) | Product identifier |
| Category | VARCHAR(20) | Product category (Furniture, Office Supplies, Technology) |
| Sub-Category | VARCHAR(20) | Product sub-category (17 values) |
| Product Name | VARCHAR(200) | Name of the product |
| Sales | DECIMAL(12,4) | Sales amount |
| Quantity | INT | Units sold |
| Discount | DECIMAL(4,2) | Discount applied, as a fraction (0.20 = 20%) |
| Profit | DECIMAL(12,4) | Profit on the row; negative means a loss |

### Known data notes

- `Order Date` and `Ship Date` come in two month-first text formats (for example `11-08-2016` and `6/16/2016`), so they are stored as text and are not used for date calculations in this project.
- `Postal Code` is stored as a number in the source file, so some New England postal codes have lost their leading zero (for example 06824 appears as 6824). It is stored as text here.
- The CSV is not UTF-8, so the load uses code page 1252.

## 2. What was done

| # | Deliverable | Purpose | SQL concepts used |
| --- | --- | --- | --- |
| 1 | [`01_table_creation.sql`](Analysis/sql/01_table_creation.sql) | Creates the `SuperstoreDB` table with one column per CSV column | `CREATE TABLE`, data types, `PRIMARY KEY`, `NOT NULL` |
| 2 | [`02_bulk_insert.sql`](Analysis/sql/02_bulk_insert.sql) | Loads the CSV into the table | `BULK INSERT`, `FORMAT`, `FIRSTROW`, `FIELDQUOTE`, `CODEPAGE` |
| 3 | [`03_value_check.sql`](Analysis/sql/03_value_check.sql) | Confirms the load worked | `COUNT(*)`, alias, `TOP`, `SELECT *` |
| 4 | [`04_audit_table.sql`](Analysis/sql/04_audit_table.sql) | Audits the descriptive columns | `DISTINCT`, `ORDER BY` on several columns |
| 5 | [`05_loss_analysis.sql`](Analysis/sql/05_loss_analysis.sql) | Finds the biggest losses and worst margins | `TOP`, `ORDER BY`, calculated column with alias, `WHERE` with `AND`, `DISTINCT` |
| 6 | [`06_discount_hypothesis.sql`](Analysis/sql/06_discount_hypothesis.sql) | Tests whether heavy discounts go with losses | Comparison operators, `AND`, `ORDER BY`, `DISTINCT` |
| 7 | [`Superstore_BRD.docx`](Analysis/Superstore_BRD.docx) | Business Requirements Document (completed) | Requirements, scope, risks, implementation plan, acceptance criteria |

### Questions each script answers

| Script | Question |
| --- | --- |
| 03 | How many rows were loaded, and what do they look like? |
| 04 | What are the categories, sub-categories, regions, ship modes, segments and states? Which discount levels exist? |
| 05 | What are the 10 biggest losses? What are the 10 worst profit margins? Which sub-categories appear among losses worse than -500? Which losses occur in the Central region and in Texas? |
| 06 | Which loss-making rows have a heavy discount (30% or more)? Which have no discount? Are any heavily discounted rows profitable? Which loss-making rows have a light discount? Which discount levels are common among losses? |

## 3. Documentation

| Document | Description |
| --- | --- |
| `README.md` | Project overview, data dictionary, scripts and how to run them |
| [`Analysis/Superstore_BRD.docx`](Analysis/Superstore_BRD.docx) | Business Requirements Document: business problem and impact, objectives, scope, stakeholders, business and functional requirements, risks, a three-phase implementation plan, benefits and acceptance criteria |

## 4. How to run

1. Install SQL Server (Developer or Express edition) and SQL Server Management Studio (SSMS).
2. Download or clone this repository.
3. Save `Analysis/Sample - Superstore.csv` in a folder that SQL Server can read, for example `C:\Data\`.
4. Open `Analysis/sql/01_table_creation.sql` in SSMS and run it. It creates the `Superstore` database and the `SuperstoreDB` table.
5. Open `Analysis/sql/02_bulk_insert.sql`, change the file path to where you saved the CSV, and run it.
6. Run `03_value_check.sql`. The table should contain **9,994 rows**.
7. Run `04_audit_table.sql`, `05_loss_analysis.sql` and `06_discount_hypothesis.sql` in order.

## 5. Repository structure

```
Superstore-Loss-and-Discount-Analysis-SQL-Server-/
|-- README.md
|-- LICENSE
`-- Analysis/
    |-- Sample - Superstore.csv
    |-- Superstore_BRD.docx
    `-- sql/
        |-- 01_table_creation.sql
        |-- 02_bulk_insert.sql
        |-- 03_value_check.sql
        |-- 04_audit_table.sql
        |-- 05_loss_analysis.sql
        `-- 06_discount_hypothesis.sql
```

## 6. Scope and limitations

- This is a Day 1 level project: it uses `SELECT`, aliases, `DISTINCT`, `TOP`, `ORDER BY` and `WHERE` only. It does not use grouping, joins or window functions.
- Comparing loss-making rows at different discount levels shows an association between discounts and losses. It does not prove that discounts caused each loss.
- The data is a single flat table with no cost columns, so deeper margin analysis is not possible here.
- The company, stakeholders and business context in the BRD are illustrative, written to practise requirements documentation on a public sample dataset.

## 7. Tools

SQL Server, T-SQL, SQL Server Management Studio (SSMS), Microsoft Word (BRD)

## 8. Possible next steps

- Group profit, sales and discount by sub-category and region with `GROUP BY`.
- Create discount bands with `CASE` and compare the loss rate in each band.
- Convert the two date formats with `TRY_CAST` and study losses by month.
- Split the flat table into customers, products and orders tables and rebuild the analysis with joins.
- Build a Power BI dashboard on top of the cleaned data.

## 9. License

This project's code is released under the MIT License (see `LICENSE`). The dataset belongs to its original source on Kaggle and is subject to that source's terms.
