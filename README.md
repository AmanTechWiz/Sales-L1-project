# Sales L1 Project

An interactive Power BI sales dashboard built from the Sample Superstore dataset. The report provides yearly and regional sales, profit, quantity, state-level performance, and year-over-year comparisons.

## Project Structure

- `Sales.pbip` - Power BI Project entry point
- `Sales.Report/` - report pages, visuals, theme, and resources
- `Sales.SemanticModel/` - tables, relationships, measures, and model metadata
- `Sample - Superstore.xls` - source workbook
- `Problem Statement.pptx` - dashboard requirements
- `Data Explained.html` - data source and field descriptions

## Open the Report

1. Install **Power BI Desktop** (current release).
2. Clone or download this repository.
3. Open `Sales.pbip` in Power BI Desktop.
4. If prompted to locate the source workbook, use `Sample - Superstore.xls`.

## Report Features

- Sales, Profit, and Quantity metric switch
- 2021-2024 year filtering
- Regional KPI cards and monthly trends
- State-level bubble map and sales ranking
- CY, PY, and YoY matrix by region and state

## Refresh and Development

The semantic model and report are stored in PBIP/PBIR text format, making changes easy to review in Git.

```powershell
# Refresh the local repository
git pull origin master

# Commit report changes
git add .
git commit -m "Update Sales report"
git push origin master
```

## Data Model and Core DAX

- **Star schema:** `Orders` is the fact table; `Date` and `People` are dimensions; `Metric` and `_Measures` are disconnected support tables.
- **Relationships:** `Orders[Order Date]` → `Date[Date]`; `Orders[Region]` → `People[Region]`.
- `Metric` supplies the Sales/Profit/Quantity slicer values and `_Measures` holds the report measures.

`Date` is a calculated table spanning the full calendar years covered by `Orders[Order Date]`. It derives `_Start` from the earliest order year and `_End` from the latest order year, then creates Year, Quarter, Month, and Month Year fields with `CALENDAR` and `ADDCOLUMNS`. It is marked as the date table for time intelligence.

### Hidden Columns

| Column | Reason |
|---|---|
| `Orders[Row ID]` | Technical source key, not a report field |
| `Orders[Order Date]` | Relationship key; users select dates through `Date` |
| `Orders[Sales]` | Reporting uses the `CY Sales` measure |
| `Orders[Quantity]` | Reporting uses the `CY Qty` measure |
| `Orders[Profit]` | Reporting uses the `CY Profit` measure |
| `Date[Month No]` | Sorting key for visible month labels |
| `Date[Month Year Sort]` | Sorting key for chronological month labels |
| `Metric[Metric Order]` | Keeps slicer values in Sales, Profit, Quantity order |
| `_Measures[Placeholder]` | Support column for the `_Measures` table |

### Core Measures

- **CY Sales:** `SUM ( Orders[Sales] )`
- **CY Profit:** `SUM ( Orders[Profit] )`
- **CY Qty:** `SUM ( Orders[Quantity] )`
- **PY Sales:** `CALCULATE ( [CY Sales], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )`
- **PY Profit:** `CALCULATE ( [CY Profit], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )`
- **PY Qty:** `CALCULATE ( [CY Qty], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )`
- **YoY Sales:** `DIVIDE ( [CY Sales] - [PY Sales], [PY Sales] )`
- **YoY Profit:** `DIVIDE ( [CY Profit] - [PY Profit], ABS ( [PY Profit] ) )`
- **YoY Qty:** `DIVIDE ( [CY Qty] - [PY Qty], [PY Qty] )`
