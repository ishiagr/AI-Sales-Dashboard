# AI-Assisted Sales Performance Dashboard

An interactive sales dashboard built with AI assistance from a raw CSV, with the data reconciled and validated before any visualization was built. The project also includes a root-cause analysis of a month where units sold rose while revenue fell.
**[Live demo](https://ishiagr.github.io/AI-Sales-Dashboard/)** 
Sample data only · Built by Ishita Agrawal ([LinkedIn](https://www.linkedin.com/in/ishita10/))



## Business problem

Sales leadership needs to see, in one place, how the business is performing and where to act: growth, profitability, product and regional mix, and channel performance. The dashboard has to answer follow-up questions without a new report each time.

## What the dashboard shows

|View|Question it answers|
|-|-|
|KPI scorecard|Revenue, profit, margin % and units, with change vs the prior month|
|Monthly trend|Is revenue growing, and is profitability improving? (September is flagged as a partial month)|
|Product performance|Which categories drive revenue and profit?|
|Regional performance|Which regions lead, and at what margin?|
|Region × product heatmap|Where are the strongest and weakest combinations?|
|Channel split|Online vs offline revenue, margin and average order value|

**Interactivity:** filters for region, product, channel and month range; a Revenue / Profit / Units toggle; click-to-filter on every chart; removable filter chips; a searchable, sortable records table; light and dark mode.

## Data

* **Source:** `data/sales-data.csv`, a sample dataset of 22 sales records from 15 Jan to 4 Sep 2025.
* **Fields:** Date, Region, Product, Sales Channel, Units Sold, Unit Price, Total Revenue, Total Cost, Profit.
* **Scope:** 4 regions, 3 products, 2 channels.
* **Note:** This is sample data. No real customer or employer data is used.

## Step 1: Data readiness (before any AI work)

|Check|Result|
|-|-|
|File structure|Semicolon-delimited, not comma-delimited. A default import produced a single column|
|Date format|D/M/YYYY. 13 of 22 dates cannot be read as M/D/YYYY, and the remaining 9 would parse without error but be wrong|
|Nulls|0 in every column|
|Duplicates|0 fully duplicated rows, 0 repeated date + region + product + channel combinations|
|Category labels|Exactly 4 regions, 3 products, 2 channels, with no spelling variants|
|Value sanity|No zero or negative units, prices or revenue|
|Revenue = Units × Price|0 mismatches (max difference 0.00)|
|Profit = Revenue − Cost|0 mismatches (max difference 0.00)|

**Control totals recorded from the source:** Revenue $428,194 · Profit $138,646 · Margin 32.4% · Units 2,316. The dashboard must match these exactly.

> These checks test the file's internal consistency. There was no second system (for example a finance ledger) to reconcile against. In a production setting, I would also tie totals to the source system.

## Step 2: Working with the AI

I wrote the brief like a specification rather than a loose request. It covered:

* the audience and the decisions the dashboard supports
* the data format (delimiter and date format)
* exact metric definitions (Margin % = Profit ÷ Total Revenue)
* layout, the six required views, and the interaction requirements
* technical constraints (single self-contained file, pinned library version, no browser storage)
* edge cases (zero-result filters, partial months)
* the control totals the output had to match

The full prompt is in [`docs/prompt.md`](docs/prompt.md).

## Step 3: Validation

I checked the dashboard against the source file independently in Excel, then tested it in the browser. Formulas and full results are in [`docs/validation-log.md`](docs/validation-log.md).

**Excel checks on the source data:** row count, blank cells, duplicates, and the two reconciliation tests (Revenue = Units × Price, and Profit = Revenue − Cost), using `COUNTA`, `COUNTBLANK`, `COUNTIFS` and `SUMPRODUCT`.

**Control totals: Excel vs dashboard**

|Metric|Expected from source|Excel result|Dashboard shows|
|-|-|-|-|
|Revenue|$428,194|\[fill in]|\[fill in]|
|Profit|$138,646|\[fill in]|\[fill in]|
|Margin|32.4%|\[fill in]|\[fill in]|
|Units|2,316|\[fill in]|\[fill in]|

**Filtered comparisons:** I also compared the dashboard with `SUMIFS` results in Excel with filters applied (West region, Electronics, August only). \[Describe the outcome: all matched, or list any differences.]

**Browser testing:** \[browser, device, date tested]. \[For example: 15 of 15 checks passed, or describe what failed.]

**Issues found and fixed**

* **Delimiter:** The CSV first loaded as a single column because it uses semicolons. I specified the delimiter, and the dashboard's parser now detects semicolon or comma.
* **A check that proved nothing:** My first null check ran on that mis-parsed single-column file. I re-ran the null, duplicate and reconciliation checks after parsing correctly.
* **Export CSV:** The button only worked inside the viewer where the page was first published. While preparing for GitHub Pages, I added a standard browser download as a fallback.
* **Partial-month flag:** It assumed the last month in the data was always partial. Now it applies only when the data ends before the 25th.



## Key insight: why revenue fell in August while units rose

Units sold rose 11.5% from July to August (425 → 474), but revenue fell 24.4% ($94,861 → $71,698).

|Driver|Effect on revenue|
|-|-|
|Volume (more units overall)|+$10.9K|
|Mix (shift away from Electronics)|−$31.1K|
|Price (lower unit prices)|−$3.0K|
|**Net change**|**−$23.2K**|

* Electronics units fell from 252 to 121 as the number of Electronics orders dropped from two to one. Electronics unit prices were flat at about $301, so this was volume, not discounting.
* Clothing and Home Goods volume more than doubled, but at much lower prices (about $60 and $150 per unit versus about $300).
* Revenue per unit fell from about $223 to about $151.
* Margin held (35.6% → 35.0%), so the issue was product mix rather than cost.

**Questions this raises:** Did an Electronics deal slip into September? Was the August Clothing and Home Goods push deliberate? Is there a stock or sales-coverage gap in Electronics?

## Limitations

* Only 22 records, so single deals swing monthly figures. Treat trends as directional.
* September is a partial month (data ends 4 Sep).
* Each CSV row is treated as one order when calculating average order value.
* The data is embedded in the page rather than loaded from a live source.
* The "Export CSV" button only appears when the page runs inside a viewer that supports file saving. It is hidden on standard web hosting.

## Moving to production

* Connect to a governed, live data source with scheduled refresh.
* Agree KPI definitions with finance and document them.
* Add automated data-quality tests and role-based access.
* Rebuild in Power BI or Tableau if the organisation's standard tool requires it.

## Tech stack

HTML, CSS and JavaScript in a single file, with [Chart.js](https://www.chartjs.org/) 4.4.1 loaded from cdnjs. AI-assisted build, human-led requirements and validation.

## Run it locally

Download `index.html` and open it in a browser. It needs an internet connection to load Chart.js.

## Repository structure

```
├── index.html            # the dashboard
├── data/
│   └── sales-data.csv    # sample data
├── docs/
│   ├── prompt.md         # the full AI brief
│   ├── validation-log.md # checks run and issues found
│   └── dashboard-screenshot.png
└── README.md
```

## Skills demonstrated

Data cleansing and reconciliation · KPI and metric definition · Dashboard design · Prompting and specifying AI work · Output validation · Price-volume-mix analysis · Root-cause analysis · Communicating limitations

## License

Code: MIT. Data: sample data for demonstration only.

