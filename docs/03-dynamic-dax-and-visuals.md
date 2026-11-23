# Dynamic DAX and Visuals

## Dynamic measures

- **Selected Metric Name** — Metric slicer selection; defaults to Sales.
  ```DAX
  SELECTEDVALUE ( Metric[Metric], "Sales" )
  ```
- **Selected Metric** — switches to the selected current-year measure.
  ```DAX
  SWITCH ( [Selected Metric Name],
      "Sales", [CY Sales],
      "Profit", [CY Profit],
      "Quantity", [CY Qty]
  )
  ```
- **Monthly Avg Selected Metric** — averages the selected metric across months; removing the month filters makes the same average repeat at every sparkline month, so its line is flat.
  ```DAX
  CALCULATE (
      AVERAGEX ( VALUES ( 'Date'[Month No] ), [Selected Metric] ),
      REMOVEFILTERS ( 'Date'[Month], 'Date'[Month No] )
  )
  ```
- **Bar Color vs Avg** — colors each monthly bar darker at/above the average and lighter below it.
  ```DAX
  IF ( [Selected Metric] >= [Monthly Avg Selected Metric], "#2B5FD9", "#B8C7EE" )
  ```
- **Card Title** — combines the visual's region with the metric and selected year.
  ```DAX
  VAR _Region = SELECTEDVALUE ( Orders[Region], "All Regions" )
  RETURN _Region & "  ·  " & [KPI Title]
  ```
- **PY Sales Label** — displays prior-year sales and its YoY direction/percentage, or n/a.
  ```DAX
  IF ( ISBLANK ( [PY Sales] ), "PY Sales: n/a",
      "PY Sales: " & FORMAT ( [PY Sales], "$#,0" )
          & "  " & IF ( [YoY Sales] >= 0, "▲ ", "▼ " ) & FORMAT ( ABS ( [YoY Sales] ), "0.0%" )
  )
  ```

## Slicers and regional KPIs

- **Year** (`Date[Year]`) is single-select (currently 2024); it filters the date context used by the page's measures.
- **Metric** (`Metric[Metric]`) is single-select (currently Profit); its selected name drives dynamic measures such as **Selected Metric** in the cards and sparklines. The **PY Sales Label** stays sales-specific; the map, state bars, and matrix also use fixed measures, not the metric switch.
- Each regional KPI block has a colored accent strip, a **Selected Metric** value card titled by **Card Title**, a **PY Sales Label** card, and a monthly combo sparkline: **Selected Metric** columns plus the **Monthly Avg Selected Metric** line. The four regions use the same measures and differ by their visual-level `Orders[Region]` filter; the strip accent color is region-specific.

## State and comparison visuals

- **Bubble map:** `Orders[State/Province]` location; bubble size is **CY Sales**; tooltips are **PY Sales** and **YoY Sales**.
- **State bar chart:** `Orders[State/Province]` category and **CY Sales** values, sorted descending by CY Sales; tooltips are **PY Sales** and **YoY Sales**.
- **CY/PY/YoY matrix:** rows are `Orders[Region]` then `Orders[State/Province]`; values are CY, PY, and YoY **Sales**, **Profit**, and **Qty** measures.
