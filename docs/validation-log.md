# Validation log

This log records how I checked the dashboard before treating it as reliable. The main method is an independent recalculation in Excel, compared against what the dashboard displays. Fill in the Result columns as you test. If anything differs from the Expected value, record it in section 6.

**Tested by:** Ishita Agrawal

## 1\. Setting up the data in Excel

1. Open a blank workbook, then **Data → From Text/CSV** and choose `sales-data.csv`.
2. Set **Delimiter** to **Semicolon** (the file is semicolon-delimited; a plain double-click can put everything into one column).
3. Use a locale that reads D/M/YYYY dates, such as English (India) or English (UK), then click **Load**.
4. Confirm the Date column holds real dates: `=ISNUMBER(A2)` returns TRUE.

The formulas below assume columns A to I are Date, Region, Product, Sales Channel, Units Sold, Unit Price, Total Revenue, Total Cost and Profit, with data in rows 2 to 23.

## 2\. Source data checks

|Check|Excel formula|Expected|Result|
|-|-|-|-|
|Row count|`=COUNTA(A2:A23)`|22|\[ ]|
|Blank cells|`=COUNTBLANK(A2:I23)`|0|\[ ]|
|First and last date|`=MIN(A2:A23)` and `=MAX(A2:A23)`|15 Jan 2025 and 4 Sep 2025|\[ ]|
|Revenue ≠ Units × Price|`=SUMPRODUCT(--(ABS(E2:E23\*F2:F23-G2:G23)>0.05))`|0|\[ ]|
|Profit ≠ Revenue − Cost|`=SUMPRODUCT(--(ABS(G2:G23-H2:H23-I2:I23)>0.05))`|0|\[ ]|
|Duplicate rows|`=SUMPRODUCT(--(COUNTIFS(A2:A23,A2:A23,B2:B23,B2:B23,C2:C23,C2:C23,D2:D23,D2:D23)>1))`|0|\[ ]|

These are internal consistency checks. There was no second system, such as a finance ledger, to reconcile against.

## 3\. Control totals: Excel vs dashboard

|Metric|Excel formula|Expected|Excel result|Dashboard shows|Match?|
|-|-|-|-|-|-|
|Revenue|`=SUM(G2:G23)`|$428,194|\[ ]|\[ ]|\[ ]|
|Profit|`=SUM(I2:I23)`|$138,646|\[ ]|\[ ]|\[ ]|
|Margin|`=SUM(I2:I23)/SUM(G2:G23)`|32.4%|\[ ]|\[ ]|\[ ]|
|Units|`=SUM(E2:E23)`|2,316|\[ ]|\[ ]|\[ ]|

## 4\. Breakdowns and filters: Excel vs dashboard

For each row, set the filter shown in the dashboard and compare the result with the Excel formula.

|What|Dashboard filter|Excel formula|Expected|Excel result|Dashboard shows|Match?|
|-|-|-|-|-|-|-|
|Electronics share of revenue|Product = Electronics|`=SUMIFS(G2:G23,C2:C23,"Electronics")/SUM(G2:G23)`|70.0%|\[ ]|\[ ]|\[ ]|
|East revenue|Region = East|`=SUMIFS(G2:G23,B2:B23,"East")`|117,116|\[ ]|\[ ]|\[ ]|
|West margin|Region = West|`=SUMIFS(I2:I23,B2:B23,"West")/SUMIFS(G2:G23,B2:B23,"West")`|30.8%|\[ ]|\[ ]|\[ ]|
|Online revenue|Channel = Online|`=SUMIFS(G2:G23,D2:D23,"Online")`|232,784|\[ ]|\[ ]|\[ ]|
|July revenue|From = Jul, To = Jul|`=SUMIFS(G2:G23,A2:A23,">="\&DATE(2025,7,1),A2:A23,"<"\&DATE(2025,8,1))`|94,861|\[ ]|\[ ]|\[ ]|
|August revenue|From = Aug, To = Aug|same, with months 8 and 9|71,698|\[ ]|\[ ]|\[ ]|
|July units|From = Jul, To = Jul|`SUMIFS` on column E, same date criteria as July revenue|425|\[ ]|\[ ]|\[ ]|
|August units|From = Aug, To = Aug|`SUMIFS` on column E, same date criteria as August revenue|474|\[ ]|\[ ]|\[ ]|

A PivotTable (Insert → PivotTable) with Region or Product in rows and the sum of Revenue and Profit in values is a fast way to check many breakdowns at once.

## 5\. Manual browser checklist

**Browser and version:** \[fill in]  **Device:** \[fill in]  **Date tested:** \[fill in]

|#|Test|Expected|Result|
|-|-|-|-|
|1|Open the live page|All four scorecards show $428,194 / $138,646 / 32.4% / 2,316|\[ ]|
|2|Region filter = West|Scorecards, charts and table all update; West Home Goods is the lowest heatmap cell|\[ ]|
|3|Product filter = Electronics|Revenue about 70% of total; regional and channel charts update|\[ ]|
|4|Click a bar in the product chart|Applies that filter; chip appears; clicking again clears it|\[ ]|
|5|Click a month in the trend chart|Filters to that month; other months dim|\[ ]|
|6|Click a heatmap cell|Filters to that region and product|\[ ]|
|7|Metric toggle: Profit, then Units|All charts switch metric; labels and tooltips update|\[ ]|
|8|Remove a chip with the ✕|That filter clears and everything updates|\[ ]|
|9|Pick filters that return no rows|"No data matches these filters" message; no broken charts|\[ ]|
|10|Reset filters|Back to the full dataset and control totals|\[ ]|
|11|Table search and column sort|Rows filter and reorder correctly|\[ ]|
|12|Export CSV with a filter on|File downloads; row count matches the table|\[ ]|
|13|Load another CSV (same columns)|Dashboard rebuilds from the new data|\[ ]|
|14|Dark mode toggle|Charts and cards stay readable|\[ ]|
|15|Narrow window or phone|Layout stacks; scorecard row scrolls sideways; nothing overflows|\[ ]|

## 6\. Issues found and fixed

|#|Issue|How I found it|Fix|
|-|-|-|-|
|1|The CSV loaded as a single column|First import of the file|Specified the semicolon delimiter and made the dashboard's parser detect semicolon or comma|
|2|My first null check proved nothing|It ran on the single-column read from issue 1, so every field was jammed together|Re-ran the null, duplicate and reconciliation checks after parsing correctly|
|3|Export CSV only worked inside the viewer where the page was first published|Reviewing what would happen on GitHub Pages|Added a standard browser download as a fallback. Needs browser test #12|
|4|The partial-month flag assumed the last month in the data was always partial|Making the page accept other CSV files|Now flagged only when the latest date in the data falls before the 25th|
|5|\[Add any difference found between Excel and the dashboard]|\[How you found it]|\[What you changed]|

## 7\. Additional check run by the AI assistant

While building the dashboard, the AI assistant extracted the dashboard's CSV parser and ran it in Node.js. It parsed all 22 rows with the correct dates, with no values failing to parse, and totals calculated from the parsed rows matched the control totals above. This tested the parser only, not the dashboard's scorecard, filter or chart functions, and I did not run it myself. Delete this section if you prefer to describe only your own checks.

## 8\. Known limitations

* 22 records, so single deals swing monthly results.
* September is a partial month (data ends 4 Sep).
* Average order value treats each CSV row as one order.
* Data is embedded in the page; it is not connected to a live source.
* The July to August price-volume-mix analysis was calculated outside Excel. Its inputs (July and August units and revenue) are checked in section 4.

