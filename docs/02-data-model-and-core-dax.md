# Data Model and Core DAX

- **Star schema:** `Orders` is the fact table; `Date` and `People` are dimensions; `Metric` and `_Measures` are disconnected support tables.
- **Relationships:** `Orders[Order Date]` -> `Date[Date]`; `Orders[Region]` -> `People[Region]`.
- `Metric` supplies the Sales/Profit/Quantity slicer values and `_Measures` holds the report measures.

## Date table

- `Date` is a calculated table spanning the full calendar years covered by `Orders[Order Date]`.
- It derives `_Start` as 1 January of the earliest order year and `_End` as 31 December of the latest order year, then uses `CALENDAR` plus `ADDCOLUMNS` to create Year, Quarter, Month No, Month, Month Year, and Month Year Sort.
- It is marked as the date table by `dataCategory: Time`; `Date[Date]` is unique and the key. This supports time-intelligence such as `SAMEPERIODLASTYEAR`.

## Hidden columns

| Column | Reason |
|---|---|
| `Orders[Row ID]` | Source row identifier; hidden because it is a technical key, not a report field. |
| `Orders[Order Date]` | Hidden after becoming the relationship key to `Date[Date]`; users select dates through `Date`. |
| `Orders[Sales]` | Hidden because reporting uses measure `CY Sales` rather than raw source aggregation. |
| `Orders[Quantity]` | Hidden because reporting uses measure `CY Qty` rather than raw source aggregation. |
| `Orders[Profit]` | Hidden because reporting uses measure `CY Profit` rather than raw source aggregation. |
| `Date[Month No]` | Hidden numeric sorting key for the visible `Month` labels. |
| `Date[Month Year Sort]` | Hidden numeric sorting key for chronological `Month Year` labels. |
| `Metric[Metric Order]` | Hidden numeric sorting key that keeps slicer values in Sales, Profit, Quantity order. |
| `_Measures[Placeholder]` | Hidden support column for the calculated one-row `_Measures` table. |

## Core measures

- **CY Sales**: `SUM ( Orders[Sales] )` sums sales in the active filter context.
- **CY Profit**: `SUM ( Orders[Profit] )` sums profit in the active filter context.
- **CY Qty**: `SUM ( Orders[Quantity] )` sums quantity in the active filter context.
- **PY Sales**: `CALCULATE ( [CY Sales], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )` uses `CALCULATE` to evaluate current-year sales under the prior-year date filter.
- **PY Profit**: `CALCULATE ( [CY Profit], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )` evaluates current profit under the prior-year date filter.
- **PY Qty**: `CALCULATE ( [CY Qty], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )` evaluates current quantity under the prior-year date filter.
- **YoY Sales**: `DIVIDE ( [CY Sales] - [PY Sales], [PY Sales] )` calculates the sales change percentage with safe divide handling.
- **YoY Profit**: `DIVIDE ( [CY Profit] - [PY Profit], ABS ( [PY Profit] ) )` calculates profit growth safely and uses `ABS` to make the prior-profit denominator positive even for losses.
- **YoY Qty**: `DIVIDE ( [CY Qty] - [PY Qty], [PY Qty] )` calculates the quantity change percentage with safe divide handling.
