# GlobalMart Superstore Supply Chain Analytics

A data cleaning and business intelligence project analyzing GlobalMart's supply chain and sales operations, using Power Query for data cleaning and Power BI for dashboarding and insight generation.

---

## Project Overview

GlobalMart, a superstore operating across 5 African regions, was facing rising customer complaints tied to delayed deliveries, simultaneous overstocking and stockouts across product categories, flat profit margins despite steady revenue, and fragmented, static reporting that was limiting real-time decision-making.

This project cleans the raw operational dataset, builds two interactive Power BI dashboards, and delivers an executive summary with data-backed recommendations.

---

## Tools Used

Microsoft Excel was used as the raw data source. Power Query handled data cleaning and transformation. Power BI Desktop was used for dashboarding, DAX measures, and visualization. PowerPoint was used for the executive summary presentation.

---

## Project Files

The raw dataset file is `GlobalMart_Raw_Dataset.xlsx`, the original uncleaned data with 2,090 records. The cleaned dataset is contains 2,010 cleaned records. The Power BI dashboard file and screenshots cover the Operations and Profitability dashboards. The power point above is the 10-slide executive presentation. This file, `README.md`, documents the full process.

---

## Data Cleaning Process (Power Query)

The raw dataset had 2,090 rows across 21 columns and multiple data quality issues. Cleaning was performed in Power Query, in the following order.

**1. Initial Load**
Home → Get Data → Excel → Transform Data, to open the Power Query Editor before any cleaning began.

**2. Removed Empty Columns**
Dropped the Notes column, which was 100% empty across all rows.

**3. Trimmed & Cleaned Text Columns**
Applied to Region, Country, Product Category, Supplier, Shipping Mode, Delivery Status, and Customer Name. Transform → Format → Trim removed leading and trailing whitespace. Transform → Format → Clean removed hidden non-printing characters. Transform → Format → Capitalize Each Word standardized casing.

**4. Standardized Spelling & Format Variants**
Used Transform → Replace Values to consolidate variants that survived trimming and casing fixes. For example, Region values like "EastAfrica" and "E Africa" were merged into "East Africa"; Supplier values like "FastTrack" and "Fasttrack" were merged into "Fasttrack Logistics"; Shipping Mode abbreviations like "Eco" and "O/N" became "Economy" and "Overnight"; and Delivery Status values like "Delayed" and "L8" became "Late".

**5. Fixed Mixed Date Formats**
Order Date and Ship Date contained two formats: DD/MM/YYYY with slash separators, and MM-DD-YYYY with dash separators. Standard Date type conversion failed on the mixed format, so a custom column was used instead, after replacing the - with / in text format and using the following formula to create the new custom column "try Date.FromText([Order Date], "en-GB")
otherwise Date.FromText([Order Date], "en-US")" so as to standardized the Us date and Uk date. All the process is done in text format and the new custom column was changed back to date format and everything was standardized with 0% error. The new column replaced the original, which was then deleted and the new column renamed back to its original name. The same process was applied to Ship Date.

**6. Removed Placeholder Junk**
Numeric columns including Quantity Ordered, Unit Cost, Unit Price, Total Sales, Total Cost, Profit, Days to Ship, Stock Level, and Damaged Goods contained a literal "?", "N/A", "nil" placeholder in place of missing data. This was replaced with blank via Transform → Replace Values, then the correct numeric type was applied.

**7. Fixed Negative Values**
Found physically invalid negative values in Quantity Ordered (19 rows, down to -20), Unit Cost (7 rows, down to -2), and Days to Ship (9 rows, down to -3). These were converted to absolute values via Transform → Number Column → Standard → Absolute Value.

**8. Recomputed Missing Financial Fields**
Rather than leaving blanks or filling with arbitrary values, missing financial fields were recalculated from related columns using Add Column → Custom Column. Total Sales was calculated as Quantity Ordered multiplied by Unit Price. Total Cost was calculated as Quantity Ordered multiplied by Unit Cost. Profit was calculated as Total Sales minus Total Cost. This was applied wherever the source fields needed to compute them were available, minimizing reliance on estimated fills.

**9. Filled Remaining Missing Values**
Categorical fields such as Region, Country, Supplier, Customer Name, Product Name, and Delivery Status were filled with "Unknown" or an equivalent label where no reliable inference was possible. Numeric fields such as Unit Price, Stock Level, Damaged Goods, Days to Ship, and any remaining gaps in Quantity Ordered or Unit Cost were filled with the column median, chosen over the mean because these fields are sensitive to outliers.

**10. Removed Duplicates**
Home → Remove Rows → Remove Duplicates on the full row set removed 32 exact duplicates. Duplicate Order IDs were also removed, keeping the first occurrence, which removed 80 additional rows.

The result was 2,090 raw records reduced to 2,010 validated, analysis-ready records, with zero missing values and zero duplicate Order IDs, and every other columns contains 100% valid and 0% error.

---

## Dashboards

**Dashboard 1: Supply Chain Operations**


**Dashboard 2: Business Performance & Profitability**


**DAX Measures Used**

\`\`\`dax
Total Orders = COUNTROWS('Raw Dataset (Messy)')

On-Time Deliveries = CALCULATE([Total Orders], 'Raw Dataset (Messy)'[Delivery Status] = "On Time")
On-Time Delivery Rate (%) = DIVIDE([On-Time Deliveries], [Total Orders], 0)

Late Deliveries = CALCULATE([Total Orders], 'Raw Dataset (Messy)'[Delivery Status] = "Late")
Late Delivery Rate (%) = DIVIDE([Late Deliveries], [Total Orders], 0)

Average Shipping Time (Days) = AVERAGE('Raw Dataset (Messy)'[Days to Ship])

Total Damaged Goods = SUM('Raw Dataset (Messy)'[Damaged Goods])
Damaged Goods Rate (%) = DIVIDE([Total Damaged Goods], SUM('Raw Dataset (Messy)'[Quantity Ordered]), 0)

Total Revenue = SUM('Raw Dataset (Messy)'[Total Sales ($)])
Total Profit = SUM('Raw Dataset (Messy)'[Profit ($)])
Profit Margin (%) = DIVIDE([Total Profit], [Total Revenue], 0)
Average Order Value = DIVIDE([Total Revenue], [Total Orders], 0)
\`\`\`

---

## Key Insights

Delivery reliability is the most urgent issue, not profitability. Only 34.4% of orders arrive on time, 65.1% are late, and shipping averages 10.2 days. Globaltrade Ng has the worst late-delivery rate at 73.0%, compared to the best performer, Primegoods Africa, at 60.0%. Even Express shipping, the most reliable mode available, is only on-time 37.1% of the time, which points to a systemic delivery problem rather than a shipping-mode issue.

Overall profit margin is a healthy 27.8%, representing $195.5M profit on $703.5M revenue, and margins are stable across categories, ranging from 27.0% to 28.7%, so no single category is dragging profitability down. Health & Beauty has the highest order volume relative to its stock level, at 92,443 units, and also the highest damaged-goods rate at 2.68%, signaling both a demand-forecasting issue and a handling or packaging issue. East Africa leads regional revenue at $157.9M, while Southern Africa trails at $122.8M, though revenue is otherwise fairly evenly distributed across product categories.

---

## Recommendations

**Review Globaltrade Ng**
73% late rate is a clear outlier — worth a supplier performance review or renegotiation.

**Reassess Express Shipping**
If it's not meaningfully more reliable than other modes, its added cost may not be justified.

**Tighten QC for Health & Beauty**
Highest damaged-goods rate in the dataset signals a handling or packaging issue despite having the highest profit and revenue.

**Rebalance Inventory**
Align stock levels with category-level demand rather than a flat average across categories.

**Standardize Data Capture**
Most cleaning issues stemmed from inconsistent manual entry — fix at the source by having a standardized format for data collection or entry.

---

## Next Steps

 Automate this dashboard's refresh cycle so leadership can monitor delivery KPIs in real time
- Extend the analysis to customer satisfaction data, if available, to link delivery delays to churn risk
- Pilot inventory rebalancing in Health & Beauty, the category with the widest demand-supply gap
---

## Author

Aishat — 400-level MBBS student, University of Ilorin. Data Analytics Fellow, IOTBTECH.
