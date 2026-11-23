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
git pull origin main

# Commit report changes
git add .
git commit -m "Update Sales report"
git push origin main
```

