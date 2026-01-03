# Data Sources 📂
This document outlines the primary data sources used for the TN Voter Analytics Dashboard project. The data is fetched directly from the Tamil Nadu Election Commission official portal.

## 🏛️ Tamil Nadu Electoral Data
The project utilizes three primary datasets to track voter demographics and electoral roll integrity throughout the year 2025.

### 1. Gender-Wise Voter Data (Baseline)
- **Snapshot Date:** 06 January 2025
- **Source:** [AC wise Electors as on Electoral Roll (06/01/2025)](https://elections.tn.gov.in/ACwise_Gendercount_06012025.aspx)
- **Description:** This dataset serves as the Starting Baseline for the 2025 analysis. It provides a breakdown of male, female, and third-gender voters across all 234 Assembly Constituencies (AC) in Tamil Nadu.

### Gender-Wise Voter Data (Year-End)
- **Snapshot Date:** 19 December 2025
- **Source:** [AC wise Electors as on Electoral Roll (19/12/2025)](https://elections.tn.gov.in/ACwise_Gendercount_19122025.aspx)
- **Description:** This dataset represents the Latest State of the electoral roll. By comparing this with the January snapshot, the dashboard calculates the net growth and demographic shifts in the voter base.

### ASD Cleanup Data (SIR 2026 Revision)
- **Dataset Type:** Audit/Quality Dataset
- **Source:** [AC wise Absent / Shifted / Dead Electors - SIR 2026](https://elections.tn.gov.in/ACwise_ASD.aspx)
- **Description:** Part of the Special Intensive Revision (SIR) 2026, this dataset identifies voters categorized as Absent, Shifted, or Deceased (ASD).
- **Analytical Use:** It is used to validate the accuracy of the voter list and visualize the removal/correction rate across different districts.

### ⚙️ Data Connectivity & ETL Note
- **Extraction Method:** Data is imported into Power BI via the Web Connector using direct `.aspx` URLs.
- **Transformation:** Power Query is utilized to:
    - Separate **AC Code** from **AC Name** (e.g., "1-Gummidipoondi").
    - Perform **Fill Down** operations to handle merged District header cells.
    - Filter out **"Total"** summary rows to prevent double-counting.
- **Update Frequency:** Data is current as of the 2025 electoral cycle revisions.

🛑 Disclaimer
This project is for analytical and educational purposes only. The data is sourced from public government records. Users should refer to the official [Elections TN](https://elections.tn.gov.in/) website for legal or official electoral purposes.