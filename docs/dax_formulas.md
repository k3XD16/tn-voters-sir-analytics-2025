# DAX Formulas Documentation

This document explains all DAX measures used in the **TN Voters Analytics – 2025 Power BI Project**. This file is organized into sections based on measure categories.

**Model Context:** Star Schema (1 Dimension, 3 Facts)

**Storage:** _Measures table

This document serves as the technical reference for all calculation logic used in the dashboard. Measures are categorized by their analytical function: **Baseline (SSR)**, **Current State (SIR)**, **Variance Analysis**, and **Audit Metrics**.

---

## 📁 Measures Structure

```plain
_Measures
├── _Metric
├── KPI - Cleanup
├── KPI - Gender
├── KPI - Overall
└── Utility & Ranking
```

---

## 📊 KPI – Cleanup

### Total Cleanup Voters

```DAX
Total Cleanup Voters =
[Total Deceased Voters] + [Total Shifted / Absent] + [Total Multiple_Enrollments]
```

### Total Deceased Voters

```DAX
Total Deceased Voters = SUM(fact_ASD[Deceased])
```

### Total Shifted / Absent

```DAX
Total Shifted / Absent = SUM(fact_ASD[Shifted_Absent])
```

### Total Multiple_Enrollments

```DAX
Total Multiple_Enrollments = SUM(fact_ASD[Enrolled_Multiple])
```

### Cleanup Rate %

```DAX
Cleanup Rate % =
DIVIDE([Total Cleanup Voters], [Total Voters SIR (Dec 2025)], 0)
```

### Cleanup Intensity

```DAX
Cleanup Intensity =
DIVIDE([Total Cleanup Voters], COUNT(dim_AC[AC_No.]), 0)
```

### Cleanup Deviation from Avg

```DAX
Cleanup Deviation from Avg =
[Cleanup Rate %] - AVERAGEX(ALL(dim_AC), [Cleanup Rate %])
```

### Expected Cleanup (State Rate)

```DAX
Expected Cleanup (State Rate) =
[Total Voters SIR (Dec 2025)] * CALCULATE([Cleanup Rate %], ALL(dim_AC))
```

### Deceased % of Total Cleanup

```DAX
Deceased % of Total Cleanup =
DIVIDE([Total Deceased Voters], [Total Cleanup Voters], 0)
```

### Shifted_Absent % of Total Cleanup

```DAX
Shifted_Absent % of Total Cleanup =
DIVIDE([Total Shifted / Absent], [Total Cleanup Voters], 0)
```

### Multiple_Enrollments % of Total Cleanup

```DAX
Multiple_Enrollments % of Total Cleanup =
DIVIDE([Total Multiple_Enrollments], [Total Cleanup Voters], 0)
```

---

## 👥 KPI – Gender

### Male Voters (Dec 2025)

```DAX
Male Voters (Dec 2025) = SUM(fact_Voters[Male])
```

### Male Voters (Jan 2025)

```DAX
Male Voters (Jan 2025) = SUM(fact_Voters[Male])
```

### Female Voters (Dec 2025)

```DAX
Female Voters (Dec 2025) = SUM(fact_Voters[Female])
```

### Female Voters (Jan 2025)

```DAX
Female Voters (Jan 2025) = SUM(fact_Voters[Female])
```

### Third Gender Voters (Dec 2025)

```DAX
Third Gender Voters (Dec 2025) = SUM(fact_Voters[Third Gender])
```

### Third Gender Voters (Jan 2025)

```DAX
Third Gender Voters (Jan 2025) = SUM(fact_Voters[Third Gender])
```

### Male Growth

```DAX
Male Growth = [Male Voters (Jan 2025)] - [Male Voters (Dec 2025)]
```

### Female Growth

```DAX
Female Growth = [Female Voters (Jan 2025)] - [Female Voters (Dec 2025)]
```

### Third Gender Growth

```DAX
Third Gender Growth = [Third Gender Voters (Jan 2025)] - [Third Gender Voters (Dec 2025)]
```

### Male Ratio SIR

```DAX
Male Ratio SIR =
DIVIDE([Male Voters (Dec 2025)], [Total Voters SIR (Dec 2025)], 0)
```

### Female Ratio SIR

```DAX
Female Ratio SIR =
DIVIDE([Female Voters (Dec 2025)], [Total Voters SIR (Dec 2025)], 0)
```

### Male Ratio SSR

```DAX
Male Ratio SSR =
DIVIDE([Male Voters (Jan 2025)], [Total Voters SSR (Jan 2025)], 0)
```

### Female Ratio SSR

```DAX
Female Ratio SSR =
DIVIDE([Female Voters (Jan 2025)], [Total Voters SSR (Jan 2025)], 0)
```

### Change in Male Ratio (pp)

```DAX
Change in Male Ratio (pp) =
[Male Ratio SSR] - [Male Ratio SIR]
```

### Change in Female Ratio (pp)

```DAX
Change in Female Ratio (pp) =
[Female Ratio SSR] - [Female Ratio SIR]
```

---

## 🌍 KPI – Overall

### Total Voters SIR (Dec 2025)

```DAX
Total Voters SIR (Dec 2025) = SUM(fact_Voters[Total])
```

### Total Voters SSR (Jan 2025)

```DAX
Total Voters SSR (Jan 2025) = SUM(fact_Voters[Total])
```

### Voters Growth (Absolute)

```DAX
Voters Growth (Absolute) =
[Total Voters SSR (Jan 2025)] - [Total Voters SIR (Dec 2025)]
```

### Voters Growth %

```DAX
Voters Growth % =
DIVIDE([Voters Growth (Absolute)], [Total Voters SIR (Dec 2025)], 0)
```

### Clean Impact %

```DAX
Clean Impact % =
DIVIDE([Total Cleanup Voters], [Total Voters SIR (Dec 2025)], 0)
```

---

## 🧮 Utility & Ranking

### Count of ACs

```DAX
Count of ACs = DISTINCTCOUNT(dim_AC[AC_No.])
```

### Count of Districts

```DAX
Count of Districts = DISTINCTCOUNT(dim_AC[District])
```

### % of Total State Voters SSR

```DAX
% of Total State Voters SSR =
DIVIDE([Total Voters SSR (Jan 2025)], CALCULATE([Total Voters SSR (Jan 2025)], ALL(dim_AC)), 0)
```

### Rank AC by Voter Loss

```DAX
Rank AC by Voter Loss =
RANKX(ALL(dim_AC), [Voters Growth (Absolute)], , ASC)
```

### Rank AC by Total Voters SSR

```DAX
Rank AC by Total Voters SSR =
RANKX(ALL(dim_AC), [Total Voters SSR (Jan 2025)], , DESC)
```

### Is Top 10 AC by Loss

```DAX
Is Top 10 AC by Loss =
IF([Rank AC by Voter Loss] <= 10, "Yes", "No")
```

### Growth Category

```DAX
Growth Category = 
VAR GrowthPct = [Voters Growth %]
RETURN
    SWITCH(
        TRUE(),
        GrowthPct >= 0, "Growth",
        GrowthPct < -0.15, "High Loss",
        GrowthPct < -0.08, "Medium Loss",
        "Low Loss"
    )
```

---

## ✅ Notes

- All percentage calculations handle division by zero gracefully using the third parameter of the `DIVIDE` function.
- Ranking functions use `ALL(dim_AC)` to ensure rankings are calculated across the entire dataset, ignoring any filters applied in the report context.
- Growth categories are defined with clear thresholds for easy segmentation in reports.
