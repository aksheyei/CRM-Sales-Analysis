# CRM Sales Pipeline Analysis

Excel-based analysis of an 8,800-deal B2B sales pipeline, built to evaluate win rate, regional performance, and — most importantly — whether closed deals are actually profitable, not just whether they close.

**Tools:** Excel (Power Query, PivotTables, Slicers, Timeline) · 30 sales agents · 85 accounts · 7 products

---

## Why this project

Most "sales dashboard" portfolio projects stop at revenue and win rate. This one goes one step further: it separates **revenue (close value)** from **profit**, and finds that the business is actually losing money on its closed-won deals overall — a result that only appears once you stop looking at top-line numbers and start splitting by region and product.

## The data

| Sheet | Rows | What it holds |
|---|---|---|
| `sales_pipeline` | 8,800 | One row per opportunity — agent, product, account, deal stage, dates, close value, profit |
| `accounts` | 85 | Customer sector, revenue, employee count, location |
| `products` | 7 | Product catalog across 3 series (GTX, MG, GTK) |
| `sales_team` | 30 | Agent → manager → regional office mapping |
| `pivots` | — | PivotTable layer feeding the dashboard |
| `Dashboard` | — | Slicer- and timeline-driven interactive view |

Time period: deals engaged Oct 2016–Dec 2017, all closes in 2017.

## Data quality issue I found and fixed

The `profit` column looked numeric but wasn't. Every deal **not** in the "Won" stage (4,562 of 8,800 rows — Lost, Engaging, Prospecting) held an **empty text string** instead of `0` or a blank number. This is the kind of bug that doesn't throw a visible error but silently turns the whole column into text, breaking any new `SUM`/`AVERAGE` formula written against it directly. The existing PivotTables happened to avoid it since they only ever aggregate `profit` for Won deals — but it needed fixing before the column could be trusted for new analysis.

Separately, 1,425 rows have a blank `account` field — not an error, just deals still in *Engaging*/*Prospecting* that haven't been matched to a confirmed account yet.

## Key findings

**Pipeline health**
- 8,800 total opportunities → 4,238 Won (48.2%), 2,473 Lost (28.1%), 1,589 Engaging, 500 Prospecting
- **Win rate (decided deals only): 63.1%**
- Total closed-won value: **₹10,005,534**
- Total profit on Won deals: **₹-17,948 — a net loss**, despite ₹10M+ in closed revenue

**Profitability splits unevenly by region**

| Region | Won Value | Profit |
|---|---|---|
| West | ₹3,568,647 | **₹-15,275** |
| Central | ₹3,346,293 | ₹-5,901 |
| East | ₹3,090,594 | **₹3,228** |

West brings in the most revenue *and* is the biggest source of loss. East is the only profitable region, despite having the lowest revenue — worth a real follow-up on discounting or cost differences between regions.

**Only 2 of 7 products are profitable on Won deals**

| Product | Series | Profit |
|---|---|---|
| GTX Plus Pro | GTX | ₹3,773 |
| MG Special | MG | ₹153 |
| GTK 500 | GTK | ₹-908 |
| MG Advanced | MG | ₹-2,482 |
| GTX Pro | GTX | ₹-3,931 |
| GTX Basic | GTX | ₹-3,987 |
| GTX Plus Basic | GTX | **₹-10,413** |

GTX is the highest-revenue series (₹7.34M of ₹10M total) but also home to the single biggest loss-making product — suggesting it may be the most heavily discounted or highest-cost line.

**Agent performance has a wide spread** — top agent Darcel Schlecht closed 349 deals worth ₹1,153,214, roughly 1.5x the next-best agent and ~10x the lowest performers.

**Revenue is seasonal**, ranging from ₹696,932 (July) to ₹1,338,466 (June), with a secondary peak in September — a pattern more consistent with quarter-end push than steady demand.

## Dashboard

The Dashboard combines a line chart (monthly trend), clustered column charts (agent/product comparisons), a pie chart (deal stage mix), and a horizontal bar chart (top/bottom agent rankings), all driven by shared PivotTables. Three slicers (Manager, Regional Office, Series) plus a Close Date timeline filter every chart at once.

## Skills demonstrated

- Relational data modeling across 6 linked sheets using Excel Tables and lookup tables
- Multi-dimensional PivotTable aggregation (stage × agent × product × series × region × time)
- Interactive, slicer-driven dashboard design (not static charts)
- Data quality auditing — catching a silent text-vs-number type bug before it could break downstream formulas
- Business interpretation — separating revenue from profitability instead of reporting vanity metrics

## Limitations & next steps

- The `profit` field's definition (margin vs. fully-loaded cost) isn't documented in the source data, so the regional/product findings show *where* losses concentrate, not *why* — confirming cost basis with finance would be the real next step
- Account-level fields (sector, revenue, employee count) aren't yet joined into the pipeline view; testing whether certain sectors/account sizes drive the West region's losses would be a natural extension
- Would extend well into Power BI for drill-through, or Python/pandas to test whether the regional profit gap is statistically significant or just a volume effect
