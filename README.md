---
# Developer & City Sales Analysis

## Table of Contents
- [Project Overview](#project-overview)
- [Project Tasks](#project-tasks)
- [Key Task Outputs](#key-task-outputs)
  - [A. Developer Share — Last 12 Months](#a-developer-share---last-12-months)
  - [B. City Share — Jun'25 Quarter](#b-city-share---jun25-quarter)
- [Important Excel Formulas](#important-excel-formulas)
  - [1. Normalize Flat Configuration → Cleaned BHK](#1-normalize-flat-configuration--cleaned-bhk)
  - [2. Developer Annual Sales Value (Last 12 Months)](#2-developer-annual-sales-value-last-12-months)
  - [3. Total Market Annual Sales Value (Last 12 Months)](#3-total-market-annual-sales-value-last-12-months)
  - [4. City Share — Jun'25 Quarter](#4-city-share---jun25-quarter)
  - [5. Weighted Average Carpet Price (Jun'25 Quarter)](#5-weighted-average-carpet-price-jun25-quarter)
- [Excel Implementation Notes](#excel-implementation-notes)
- [Excel Perspective Insights](#excel-perspective-insights)
- [Next Steps](#next-steps)
- [Author & Tools](#author--tools)

---

## Project Overview
This project analyzes 1–3 BHK real-estate sales to quantify developer market share (last 12 months) and city-level contribution in the Jun’25 quarter. Deliverables include reproducible Excel pivots (formula-based), CSV exports, charts, and a 4-slide PPT summarizing key findings. All pivot computations are done with explicit formulas (SUMIFS / SUMPRODUCT) so results are transparent and auditable.

---

## Project Tasks
1. **Developer-wise Annual Sales Value (Last 12 Months)** — compute each developer’s total sales value and % share (1–3 BHK only).  
2. **City-wise Quarterly Sales Value (Jun’25 Quarter)** — compute city totals and share (Apr–Jun 2025).  
3. **Pivot Table Implementation** — build formula-based pivot sheets (SUMIFS / SUMPRODUCT) for reproducibility.  
4. **BHK Extraction & Normalization** — standardize messy configuration text into `CleanedBHK` (1BHK/2BHK/3BHK/Studio/Other).

---

## Key Task Outputs

### A. Developer Share — Last 12 Months
**Latest date:** `05-10-2025` | **Cut-off date:** `05-10-2024`  
**Total Sales Value (1–3 BHK): ₹ 2,872.826 Cr**

| Developer   | Sales Value (Cr) | Share (%) |
|--------------|-----------------:|----------:|
| Builder 6    | 705.989          | 24.57%    |
| Builder 3    | 463.675          | 16.14%    |
| Builder 12   | 301.053          | 10.48%    |
| Builder 1    | 285.155          | 9.93%     |
| Builder 4    | 275.110          | 9.58%     |
| Builder 15   | 248.064          | 8.63%     |
| Builder 2    | 163.404          | 5.69%     |
| Builder 16   | 146.751          | 5.11%     |
| Builder 8    | 66.299           | 2.31%     |
| Builder 14   | 58.963           | 2.05%     |
| Builder 7    | 49.680           | 1.73%     |
| Builder 13   | 46.799           | 1.63%     |
| Builder 5    | 21.290           | 0.74%     |
| Builder 9    | 0.000            | 0.00%     |
| Builder 11   | 0.000            | 0.00%     |
| **Total**    | **2872.826**     | **100.00%** |

**Insight:** Top 3 developers (Builders 6, 3, 12) ≈ **51.2%** of annual value. Top 5 ≈ **70.7%** — notable concentration.

---

### B. City Share — Jun'25 Quarter
**Period:** Apr–Jun 2025  
**Total Jun’25 Quarterly Sales Value:** ₹ 717.805 Cr

| City       | Quarterly Sales (Cr) | Share (%) |
|------------|---------------------:|----------:|
| NCR        | 315.224              | 43.92%    |
| Goa        | 148.014              | 20.62%    |
| Hyderabad  | 143.108              | 19.94%    |
| MMR        | 95.484               | 13.30%    |
| Bangalore  | 5.938                | 0.83%     |
| Vizag      | 5.046                | 0.70%     |
| Pune       | 4.990                | 0.70%     |
| **Total**  | **717.805**          | **100.00%** |

**Insight:** NCR + Goa + Hyderabad ≈ **84.5%** of Jun’25 sales — strong regional clustering.

---

## Important Excel Formulas

> **Notation / column mappings used in examples**  
> `Data` sheet columns: `A` = date (AsOnDate/Qtr), `G` = Developer, `H` = CleanedBHK, `L` = Quarterly Sales Value (Cr), `N` = Unsold Units, `Q` = Total Supply (Units), `Z` = Quarterly Sales (Units), `K` = Carpet Rate, `M` = Unsold Carpet Area.

### 1. Normalize Flat Configuration → Cleaned BHK
```excel
=IF(
  ISNUMBER(SEARCH("1BHK",A2)),"1BHK",
  IF(
    OR(ISNUMBER(SEARCH("2BHK",A2)),ISNUMBER(SEARCH("2 1/2",A2)),ISNUMBER(SEARCH("2 ½",A2))),"2BHK",
    IF(
      OR(ISNUMBER(SEARCH("3BHK",A2)),ISNUMBER(SEARCH("3 1/2",A2)),ISNUMBER(SEARCH("3 ½",A2))),"3BHK",
      IF(ISNUMBER(SEARCH("4BHK",A2)),"4BHK",
        IF(OR(ISNUMBER(SEARCH("STUDIO",A2)),ISNUMBER(SEARCH("1RK",A2))),"Studio",
          IF(ISNUMBER(SEARCH("PENT",A2)),"Penthouse","Other")
        )
      )
    )
  )
)
````

*Maps variants like “2 1/2 BHK” → `2BHK`. Adjust if you want to preserve half-BHK as 2.5/3.5.*

---

### 2. Developer Annual Sales Value (Last 12 Months) — per developer (1–3 BHK)

Assume `E1=LatestDate`, `E2=CutoffDate`, developer name in `$A2`:

```excel
=SUMIFS(Data!$L:$L,Data!$G:$G,$A2,Data!$A:$A,">="&$E$2,Data!$A:$A,"<="&$E$1,Data!$H:$H,"1BHK")
+SUMIFS(Data!$L:$L,Data!$G:$G,$A2,Data!$A:$A,">="&$E$2,Data!$A:$A,"<="&$E$1,Data!$H:$H,"2BHK")
+SUMIFS(Data!$L:$L,Data!$G:$G,$A2,Data!$A:$A,">="&$E$2,Data!$A:$A,"<="&$E$1,Data!$H:$H,"3BHK")
```

---

### 3. Total Market Annual Sales Value (Last 12 Months) — all developers (1–3 BHK)

```excel
=SUMIFS(Data!$L:$L,Data!$A:$A,">="&$E$2,Data!$A:$A,"<="&$E$1,Data!$H:$H,"1BHK")
+SUMIFS(Data!$L:$L,Data!$A:$A,">="&$E$2,Data!$A:$A,"<="&$E$1,Data!$H:$H,"2BHK")
+SUMIFS(Data!$L:$L,Data!$A:$A,">="&$E$2,Data!$A:$A,"<="&$E$1,Data!$H:$H,"3BHK")
```

---

### 4. City Share — Jun'25 Quarter (per city, 1–3 BHK)

Replace `CityCol` and `$B2` appropriately:

```excel
=SUMIFS(Data!$L:$L,Data!$CityCol:$CityCol,$B2,Data!$A:$A,">="&DATE(2025,4,1),Data!$A:$A,"<="&DATE(2025,6,30),Data!$H:$H,"1BHK")
+SUMIFS(...,Data!$H:$H,"2BHK")
+SUMIFS(...,Data!$H:$H,"3BHK")
```

---

### 5. Weighted Average Carpet Price — per tower for Jun'25

```excel
=IFERROR(
 SUMPRODUCT(
  (Data!$C$2:$C$N=$A2)*
  (Data!$A$2:$A$N>=DATE(2025,4,1))*
  (Data!$A$2:$A$N<=DATE(2025,6,30))*
  (Data!$K$2:$K$N)*
  (Data!$M$2:$M$N)
 )
 /
 SUMPRODUCT(
  (Data!$C$2:$C$N=$A2)*
  (Data!$A$2:$A$N>=DATE(2025,4,1))*
  (Data!$A$2:$A$N<=DATE(2025,6,30))*
  (Data!$M$2:$M$N)
 ),
 "NA")
```

*Replace `$N` with actual last row or use table structured references.*

---

## Excel Implementation Notes

* Convert raw data to an **Excel Table** for stable structured references.
* Define named ranges: `LatestDate`, `CutoffDate` for clarity in formulas.
* Use helper columns: `CleanedBHK`, `QuarterLabel`.
* Pivot layout recommendation: Rows → Developer; Columns → QuarterLabel; Values → Supply / Sales / Unsold; Filter → CleanedBHK (select 1BHK–3BHK).
* Validate pivot outputs with `SUMIFS` spot checks.
* For `SUMPRODUCT` use bounded ranges or structured references (avoid full-column references).

---

## Excel Perspective Insights

* **Concentration risk:** Top 5 developers ≈ 70.7% of annual sales — monitor dependency.
* **Geographic hotspots:** NCR, Goa, Hyderabad jointly ≈ 84.5% of Jun’25 — prioritize these markets.
* **Data gaps:** Builders 9 & 11 show zero sales — confirm inactivity vs. missing data.
* **Excel actions:** Create KPI cards (Total Sales L12M, Jun’25 Sales, Top Dev, Top City), add slicers for `CleanedBHK`, and add a Pareto chart to visualize developer concentration.

---

## Next Steps

* Automate pivot regeneration (Python / VBA).
* Add QoQ developer trend visuals and absorption rate dashboards.
* Consider preserving 2.5/3.5 BHK as separate categories if required by stakeholders.

---

## Author & Tools

**Author:** *Shreya Pandey*
**Tools used:** Microsoft Excel, PowerPoint
**Last updated:** Oct 2025

```
- Produce a one-line GitHub repo description to match this README. Which would you prefer?
```
