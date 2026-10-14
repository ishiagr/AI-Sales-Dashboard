# Prompts used to build the dashboard

This file records the prompts behind the project, in the order I used them. The first is the main specification; the rest are follow-ups that refined the result or extended the analysis. Wording is lightly tidied for typos only.

## How the prompts are structured

I write the first prompt as a specification rather than a loose request. Each section has a job:

|Section|Purpose|
|-|-|
|Data notes|Stops the AI misreading the file: semicolon delimiter, D/M/YYYY dates, column names, row counts|
|Control totals|Gives a way to check the output: totals the dashboard must match|
|Layout|Fixes the structure so the result is predictable|
|Visualizations|Names the six views and what each must show|
|Interactivity|Lists the behaviour I need, so it isn't left to the AI's defaults|
|Technical requirements|Sets constraints: single file, pinned library version, no browser storage, edge cases|

## Prompt 1: Build the dashboard

```
Build a single-file, self-contained HTML sales performance dashboard from the attached CSV (sales-data.csv).

DATA NOTES
- The file is semicolon-delimited (;), not comma-delimited.
- Dates are in D/M/YYYY format (e.g. 15/1/2025 = 15 January 2025).
- Columns: Date, Region, Product, Sales Channel, Units Sold, Unit Price, Total Revenue, Total Cost, Profit.
- There are 22 rows (Jan-Sep 2025), 4 regions (North, South, East, West), 3 products (Electronics, Clothing, Home Goods) and 2 channels (Online, Offline).
- Embed the data directly in the HTML as a JS array so the file works offline with no uploads. Calculate Margin % as Profit / Total Revenue \* 100.
- Sanity check your totals against these: Revenue $428,194, Profit $138,646, Margin 32.4%, Units 2,316.

LAYOUT
- Full-width layout that uses the whole browser window (no narrow centered column). Use CSS grid with a max-width of at least 1800px or 100% width.
- Top row: header with title, date range, and a filter bar.
- Second row: 4 KPI cards spread evenly across the full width.
- Below: a 3-column grid on desktop (2 columns on tablet, 1 on mobile) with the charts below.
- Clean, modern look with card-based panels, subtle shadows, rounded corners and generous spacing. Support both light and dark mode (follow the system setting, plus a manual toggle).

THE 6 VISUALIZATIONS
1. KPI scorecard: Total Revenue, Total Profit, Margin %, Units Sold. Each card shows the value and a small trend indicator comparing the latest month with the previous month.
2. Monthly trend combo chart: revenue as bars and margin % as a line on a secondary axis, by month. Flag September as a partial month (e.g. a lighter bar or a note).
3. Revenue and profit by product: grouped bar chart. Show each product's share of revenue (Electronics is about 70%) in the tooltip.
4. Regional performance: horizontal bar chart of revenue by region with margin % shown as a marker or label.
5. Region x Product heatmap: a grid of revenue (rows = region, columns = product) with a color scale. Show the dollar value in each cell and highlight the lowest cell (West / Home Goods).
6. Channel split: donut chart of Online vs Offline revenue with a small table beside it showing margin % and average order value for each channel.

INTERACTIVITY
- Global filters (Region, Product, Channel, and a date range or month selector). Every KPI and chart must update instantly when a filter changes. Include a "Reset filters" button.
- Click-to-filter: clicking a bar, slice or heatmap cell applies it as a filter, and clicking it again clears it. Show active filters as removable chips.
- Hover tooltips with formatted values ($ with thousands separators, % to 1 decimal).
- A metric toggle (Revenue / Profit / Units) that switches what the charts display.
- A sortable, searchable data table at the bottom showing the filtered rows, with CSV export of the filtered data.
- Smooth animated transitions when charts update.

TECHNICAL REQUIREMENTS
- One .html file with all CSS and JS inline. The only external scripts allowed are from https://cdnjs.cloudflare.com, pinned to an exact version (e.g. Chart.js 4.4.1). No other external resources.
- Use a system font stack as the font.
- Do not use localStorage or sessionStorage; keep all state in JS variables.
- Make charts responsive and resize with the window.
- Handle edge cases: if filters return zero rows, show a friendly "No data matches these filters" message instead of broken charts.

When finished, briefly list what you built and any assumptions you made.
```

## Prompt 2: Root-cause analysis

```
Units sold were higher in August, an 11.5% increase from July, but revenue dipped. Why? Do the root cause analysis.
```

**Result:** a volume / mix / price decomposition of the $23.2K revenue decline (volume +$10.9K, mix −$31.1K, price −$3.0K), traced to fewer high-priced Electronics orders replaced by lower-priced Clothing and Home Goods volume.

## Prompt 3: Scorecards and CSV export

```
Edit the dashboard. Give me the scorecards on the dashboard and also create the CSV file export code.
```

**Result:** four additional scorecards (Orders, Average order value, Top region, Top product) and an Export CSV button that downloads the filtered rows.

## Prompt 4: Scorecard layout

```
I want the scorecards in a single row with different columns, like placards, for revenue, profit and so on.
```

**Result:** scorecards restyled as a single-row strip of placards, each with its own colour accent.

## Prompt 5: Full-width trend chart and CSV-driven build

```
Create a visual representation of a complete dashboard with interactive elements.
- Make the line chart stretch across the full width of the dashboard.
- Use the information from the CSV I uploaded to populate the charts.
- Present it as an HTML/CSS artifact showing how the dashboard should look.
- Use Chart.js for the charts, and include JavaScript to process the CSV file and build the visualizations.

\[Followed by the six recommended visualizations and the two data caveats from the earlier analysis.]
```

**Result:** the final dashboard (`index.html`): four scorecards in one row, a full-width monthly trend chart, and the CSV text parsed in JavaScript at load, with a control to load another CSV with the same columns.

## What I would improve in the prompts

* Put the control totals in a prompt line that asks the AI to stop and report if its totals don't match, instead of just "sanity check".
* Ask the AI to list the checks it ran and anything it could not verify.
* State the audience and the decisions the dashboard supports at the top, not only the visuals.

