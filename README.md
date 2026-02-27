# 📊 S&P 500 Power BI Report

A multi-page interactive Power BI report analyzing S&P 500 components using data sourced from Barchart.com. The report explores sector performance, dividend yield, market capitalization, and company-level financial metrics.

---

## 📁 Project Structure

```
├── sp-500-index-02-21-2026.csv   # Raw data exported from Barchart.com
├── Week_8_Project.pbix           # Power BI report file
└── README.md                     # Project documentation
```

---

## 🗃️ Data Source

- **Source:** [Barchart.com](https://www.barchart.com) — S&P 500 Components
- **Date:** February 21, 2026
- **Records:** 501 companies (after data cleaning)
- **Custom View Fields:**
  - Sector, Industry, Time
  - YTD % Change, 10Y % Change, 52W % Change
  - 60-Month Beta, Market Capitalization
  - Annual Sales, Annual Net Income
  - Earnings Per Share (TTM)
  - Annual Dividend Yield

---

## ⚙️ Data Preparation (Power Query)

The following transformations were applied in Power Query before building the report:

| Transformation | Details |
|---|---|
| Removed footer row | Barchart appends a credit line as the last row — removed via "Remove Bottom Rows" |
| Sales → Sales $M | Divided by 1,000,000 and renamed column |
| Net Income → Net Income $M | Divided by 1,000,000 and renamed column |
| Market Cap → Market Cap $B | Divided by 1,000,000,000 and renamed column |
| % Columns cleaned | Removed `+`, `%`, `,` characters from YTD, 10Y, 52W, Dividend Yield columns |
| N/A values replaced | Replaced `N/A` text with null/blank before type conversion |
| Data types set | All numeric columns set to Decimal Number; Time set to Date |
| Error rows removed | Remaining error rows in Beta and 52W columns removed (<1% of data) |

---

## 📐 DAX Measures

Two custom measures were created to calculate true sector-level averages for the scatter chart:

```dax
Beta_Average = 
AVERAGEX(
    VALUES('sp-500-index-02-21-2026'[Sector]),
    CALCULATE(AVERAGE('sp-500-index-02-21-2026'[Beta]))
)
```

```dax
Dividend_Yield_Average = 
AVERAGEX(
    VALUES('sp-500-index-02-21-2026'[Sector]),
    CALCULATE(AVERAGE('sp-500-index-02-21-2026'[Dividend Yield]))
)
```

> **Why AVERAGEX instead of AVERAGE?**  
> Using a simple `AVERAGE` in a sector-level chart would compute a weighted average across all rows, not a true per-sector average. `AVERAGEX` iterates over each sector and computes the average independently, giving accurate results in the scatter chart.

---

## 📄 Report Pages

### Page 1 — S&P 500 Report (Navigation)
The landing page of the report. Contains:
- Report title
- Three navigation buttons linking to each report section:
  - **Sector Overview**
  - **Relative Performance**
  - **Company Profile**

---

### Page 2 — Sector Overview
Answers the question: *Which sector offers the highest dividends with the lowest risk (Beta)?*

**Visuals:**
- **Sector Slicer** — filter the page by sector
- **Scatter Chart** — Average Beta (X-axis) vs Average Dividend Yield (Y-axis) by Sector
  - Sectors in the top-left quadrant (low Beta, high Yield) are the most attractive for income investors
- **Pie Chart** — Market Capitalization ($B) by Sector
  - Computers & Technology dominates at ~44% of total S&P 500 market cap

**Key Insight:**  
Aerospace and Transportation sectors offer relatively high dividend yields with lower Beta compared to tech-heavy sectors, making them attractive for risk-averse income investors.

---

### Page 3 — Relative Performance
Focuses on the **Top 3 companies by Dividend Yield** across the S&P 500.

**Top 3 Companies:**
| Symbol | Sector | Dividend Yield |
|---|---|---|
| LYB | Basic Materials | 9.90% |
| CAG | Consumer Staples | 7.49% |
| DOC | Finance | 7.15% |

**Visuals:**
- **Table** — Top 3 companies by Dividend Yield (Symbol, Sector, Dividend Yield)
- **Card** — Average Year to Date % Change for the Top 3 companies (5.91%)
- **Clustered Bar Chart** — Sales $M and Net Income $M for the Top 3 companies
  - LYB has the highest Sales ($30.2B) but reported a net loss (-$738M)
  - CAG has the strongest positive Net Income ($1.15B)

---

### Page 4 — Company Profile
Profiles the standout company from the Top 3: **CAG (Conagra Brands)**

**Why CAG?**
| Criteria | CAG | DOC | LYB |
|---|---|---|---|
| Beta | **-0.01** ✅ Lowest | 1.12 | 0.70 |
| Dividend Yield | 7.49% | 7.15% | **9.90%** |
| Sales | $11.6B | $2.8B | **$30.2B** |
| Net Income | **$1.15B** ✅ Only profitable one | $71M | -$738M ❌ |

CAG wins on **lowest Beta** (near-zero market correlation) and is the **only profitable company** among the three, making it the most well-rounded pick despite not having the highest dividend yield.

**Visuals:**
- **Cards** — Symbol (CAG), Sector (Consumer Staples), Earnings TTM ($1.91), Date (Feb 20, 2026)
- **Slicer** — Symbol filter set to CAG (controls all cards)
- **Treemap** — Top 10 companies in the Consumer Staples sector by Market Cap ($B)
  - PG (Procter & Gamble) and KO (Coca-Cola) are the dominant players in the sector

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Barchart.com** | Data source for S&P 500 components |
| **Power BI Desktop** | Data modeling, DAX measures, report building |
| **Power Query** | Data cleaning and transformation |

---

## 💡 Key Takeaways

- **Computers & Technology** dominates the S&P 500 by market cap (~44%) but offers among the lowest dividend yields
- **Aerospace and Basic Materials** sectors offer higher dividend yields but come with varying Beta risk profiles
- **CAG (Conagra Brands)** stands out among high-dividend stocks as the most stable option with near-zero Beta and consistent profitability
- High dividend yield alone is not sufficient — **LYB** had the highest yield (9.90%) but posted a net loss, highlighting the importance of analyzing multiple financial metrics together

---

## 📌 Notes

- The dataset does not include a company **Name** column — only ticker symbols are available from the Barchart export
- **10Y % Change** has ~39 null values for newer companies without a 10-year trading history (e.g., ABNB)
- **Last Price** is not included in the dataset as Barchart did not export it in the custom view
