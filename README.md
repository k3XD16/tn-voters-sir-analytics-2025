<!-- Disclaimer Banner -->
> ⚠️ **DISCLAIMER:** ⚠️ This project uses simulated electoral data for **educational and portfolio purposes only**. It is not intended for official or production use.

---

# Tamil Nadu Electoral Roll Analysis 2025

A comprehensive Power BI analytics dashboard analyzing the Tamil Nadu Electoral Roll Revision, comparing Special Summary Revision (SSR) and Special Intensive Revision (SIR) data across 38 districts and 234 assembly constituencies.

**Status:** ✅ Complete | **Last Updated:** January 3, 2026


## 📋 Table of Contents

- [Dashboard Glimpse](#-dashboard-glimpse)
- [Overview](#-overview)
- [Data Transformation](#-data-transformation)
- [Key Features](#-key-features)
- [Dashboard Pages](#-dashboard-pages)
- [Project Structure](#-project-structure)
- [Technical Stack](#-technical-stack)
- [Data Model](#-data-model)
- [Key Metrics](#-key-metrics)
- [Documentation](#-documentation)
- [Contact & Support](#-contact--support)


## 📋 Dashboard Glimpse

### Executive Overview

<img src="assets/P1%20-%20Executive%20Overview.png" width="700">

### District Drill-Down

<img src="assets/P4%20-%20District%20Detail.png" width="700">

---

## 🎯 Overview

This project analyzes the 2025 Tamil Nadu electoral roll revision, examining:

- **Voter Count Changes:** 64M (SSR, Jan 2025) → 54M (SIR, Dec 2025) = **-14.52% change**
- **Cleanup Impact:** 9.24M voters removed (**15.31%** of original roll)
- **Gender Representation:** Stable ~47% female ratio (no bias detected)
- **Geographic Variation:** District-level and AC-level breakdowns

**Timeframe:** January 6, 2025 – December 19, 2025  
**Geographic Scope:** 38 districts, 234 assembly constituencies, entire Tamil Nadu state

---
## 🛠️ Data Transformation

### Raw Data Sources

<img src="assets/data-transform/SSR Jan 2025 raw.png" width="500">

### Cleaned Data After Power Query ETL

<img src="assets/data-transform/SSR Jan 2025 cleaned.png" width="500">

The Power Query transformations were applied to clean and structure the data for analysis. It was attached as documentation in the `docs/power_query_steps.md` file.


## ✨ Key Features

### 1. **Interactive Navigation Sidebar**

- Hamburger menu for compact UX
- Quick access to 5 analysis pages
- Consistent styling across report

### 2. **5 Comprehensive Pages**

- **Executive Overview** – High-level KPIs and state trends
- **Gender Analysis** – Demographic shifts and ratio analysis
- **Cleanup Analysis** – Breakdown by removal category (deceased, shifted, duplicates)
- **District Drill-Down** – AC-level deep dive with mapping
- **Information** – Dashboard metadata and documentation

### 3. **35+ DAX Measures**

- Core voter metrics (SSR, SIR, growth)
- Gender-specific calculations
- Cleanup analysis by category
- Advanced ranking and categorization
- Performance-optimized formulas

### 4. **Professional Design**

- Responsive layouts adapting to filters
- Color-coded KPI cards
- Intuitive visualizations (stacked charts, treemaps, area charts)
- Data quality badges

### 5. **Complete Documentation**
- DAX formula reference
- Information page with FAQ
- Recent updates timeline
- Data quality indicators

---

## 📊 Dashboard Pages

### Page 1: Executive Overview

**What it does:** State-level summary with key metrics and district comparison

**Visualizations:**

- KPI Cards: SSR (64M), SIR (54M), Loss (-9.24M), Cleanup Impact (15.31%)
- Stacked bar chart: Gender composition (SSR vs SIR)
- Horizontal bar chart: Voter loss by district (top 6 impacted)
- Area chart: Voter count comparison by constituency

**Key Interactions:** District & AC slicers for filtering



---

### Page 2: Gender Analysis

**What it does:** Examine gender representation changes and detect bias

**Visualizations:**
- Stacked column chart: Male/Female/Third Gender by district (SSR vs SIR)
- Data matrix: Gender ratios and percentage point changes
- KPI cards: Male/Female growth metrics
- Conditional formatting: Green (stable), Yellow (small shift), Red (significant shift)

**Business Value:** Confirms cleanup process was non-discriminatory

---

### Page 3: Cleanup Analysis

**What it does:** Break down removal categories and identify cleanup patterns

**Visualizations:**
- 100% stacked bar chart: Cleanup composition by district (Deceased, Shifted, Duplicates)
- Treemap: Total cleanup volume by district with color-coded intensity
- Stacked bar chart: Cleanup type percentage breakdown
- Data table: Cleanup statistics and rates

**Business Value:** Identify which districts have highest duplicate/shifted voter issues

---

### Page 4: District Drill-Down

**What it does:** Interactive AC-level analysis for deep investigation

**Visualizations:**

- Slicer: Select district to filter to all ACs
- Clustered column chart: Voter counts (SSR vs SIR) for each AC
- Data matrix: AC metrics (growth %, cleanup rate, cleanup intensity, rank)
- Map: Geographic visualization of selected district

**Business Value:** Pinpoint specific constituencies needing further investigation

---

### Page 5: Information

**What it does:** Self-documenting metadata and user support

**Content:**

- Project overview and data scope
- Key metrics summary
- Page guide (what each page does)
- Recent updates timeline
- Data quality status badges
- Contact information

---

## 🏗️ Project Structure

```plain
TN-voters-SIR-Analytics-2025/
├── README.md                          
├── TN_Voters_Analysis_2025-26.pbix   
│
├── docs/
│   ├── dax_formulas.md               
│   ├── data_model.md    
│   ├── data_sources.md
│   └── power_query_steps.md
│
├── data/
│   ├── tn-voters-datasets-cleaned.xlsx
│
└── assets/
    ├── data_model.png
    ├── data-source.png
    ├── executive_overview.png
    ├── gender_analysis.png
    ├── cleanup_analysis.png
    ├── district_detail.png
    └── information_page.png
    
```

---

## 🛠️ Technical Stack

| Component | Technology | Version |
| :--- | :--- | :--- |
| **BI Platform** | Power BI Desktop | Latest (2025) |
| **Data Modeling** | DAX (Data Analysis Expressions) | Power BI Standard |
| **ETL** | Power Query | Built-in Power BI |
| **Database** | Star Schema (In-memory) | Fact + Dimension tables |
| **Visualizations** | Power BI Native Visuals | Cards, Charts, Maps |
| **Version Control** | Git + GitHub | Standard workflow |

---

## 🗄️ Data Model

### Star Schema Architecture

```plain
Fact Tables:
├── fact_Voters
│   ├── AC_No.
│   ├── Snapshot_Date
│   ├── Snapshot_Revision          -- SSR / SIR flag
│   ├── Male
│   ├── Female
│   ├── Third_Gender
│   └── Total                      -- Total voters by AC & snapshot
│
└── fact_ASD
    ├── AC_No.
    ├── Snapshot_Date
    ├── Deceased
    ├── Enrolled_Multiple_Places   -- Duplicate registrations
    ├── Shifted_Absent             -- Shifted / not found
    └── Total                      -- Total cleanup voters by AC & snapshot


Dimension Tables:
└── dim_AC
    ├── AC_No.
    ├── AC_Name
    └── District                   -- District name for each AC
```

### Key Relationships

- `dim_District` 1:N `dim_AC`
- `dim_AC` 1:N `fact_Voters`
- `dim_AC` 1:N `fact_Cleanup`

---

## 📈 Key Metrics

### Core Metrics

| Metric | Value | Significance |
| :--- | :--- | :--- |
| **SSR Total (Jan 2025)** | 64M | Baseline voter count |
| **SIR Total (Dec 2025)** | 54M | Final voter count post-cleanup |
| **Voter Loss (Absolute)** | -9.24M | Total removed voters |
| **Voter Loss (%)** | -14.52% | Cleanup impact as % of SSR |
| **Cleanup Rate** | 15.31% | Standard benchmark |
| **Female Ratio (SSR)** | ~47.5% | Gender representation (baseline) |
| **Female Ratio (SIR)** | ~47.5% | Gender representation (final) |
| **Districts Analyzed** | 38 | Complete state coverage |
| **ACs Analyzed** | 234 | Full AC coverage |

### Top 3 Most Impacted Constituencies

1. **Chennai** – Lost 1.44M voters
2. **Chengalpattu** – Lost 660K voters
3. **Salem** – Lost 330K voters

---

## 📞 Contact & Support

**For questions about this project:**
- 📧 Email: mohamedkhasim.16@gmail.com
- 🔗 GitHub: [https://github.com/k3XD16](https://github.com/k3XD16)
- 💼 LinkedIn: [linkedin.com/in/mohamedkhasim16](https://www.linkedin.com/in/mohamedkhasim16)

**For suggestions or improvements:**
- Open an issue on GitHub
- Submit a pull request with improvements

### External Resources

- [Microsoft DAX Documentation](https://learn.microsoft.com/en-us/dax/)
- [Power BI Best Practices](https://learn.microsoft.com/en-us/power-bi/)
- [Data Modeling Concepts](https://learn.microsoft.com/en-us/power-bi/guidance/star-schema)

---

<div align="center">

**Made with ❤️ for Data Excellence**

Last updated: January 3, 2026 | Power BI 2025

</div>

---
