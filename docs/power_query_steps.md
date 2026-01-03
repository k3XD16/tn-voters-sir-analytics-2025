# Power Query Transformations ⚙️

## 1. Fact_Voters_SSR_(Jan2025)

**Purpose:** Jan 2025 voter snapshot (Baseline).

### Key Steps

- Web import from TN Elections site.

- Removed subtotal rows.

- **Data Cleaning:** Split District and AC columns to isolate codes.

- **Standardization:** Renamed `AC No.` to `AC_No.` for consistency.

- **Snapshot:** Added `Snapshot_Date = 2025-01-06`.

### M Code

```m
let
    // 1. Source & Row Filtering
    Source = #"tbl_AC_GenderCount_Jan25-raw",
    RemoveTotals = Table.SelectRows(Source, each ([No. and Name of the AC] <> "Grand Total" and [No. and Name of the AC] <> "Total")),
    
    // 2. District Cleaning (Fill Down -> Split -> Clean)
    FillDistrict = Table.FillDown(RemoveTotals, {"No. and Name of the District"}),
    SplitDistrict = Table.SplitColumn(FillDistrict, "No. and Name of the District", Splitter.SplitTextByCharacterTransition({"0".."9"}, (c) => not List.Contains({"0".."9"}, c)), {"District_Code", "District"}),
    CleanDistrict = Table.ReplaceValue(SplitDistrict, "-", "", Replacer.ReplaceText, {"District"}),
    
    // 3. AC Number & Name Extraction
    RemoveCols = Table.RemoveColumns(CleanDistrict, {"Sl No", "District_Code"}),
    SplitAC = Table.SplitColumn(RemoveCols, "No. and Name of the AC", Splitter.SplitTextByCharacterTransition({"0".."9"}, (c) => not List.Contains({"0".."9"}, c)), {"AC_No.", "AC_Name"}),

    // 4. Standardize AC Names (Chained Replacements)
    FixACNames = Table.TransformColumns(SplitAC, {
        {"AC_Name", each 
            let 
                Step1 = Text.Replace(_, "-Thiru-Vi-Ka-Nagar (SC)", "-Thiru. Vi. Ka. Nagar (SC)"),
                Step2 = Text.Replace(Step1, "-Chepauk-Thiruvallikeni", "-Chepauk Thiruvallikeni"),
                Final = Text.Replace(Step2, "-", "") // Remove remaining hyphens
            in Final, 
            type text
        }
    }),

    // 5. Final Schema & Typing
    AddDate = Table.AddColumn(FixACNames, "Snapshot_Date", each #date(2025, 1, 6), type date),
    RenameCols = Table.RenameColumns(AddDate, {{"Third Gender", "Third_Gender"}}),
    FinalTypes = Table.TransformColumnTypes(RenameCols,{
        {"AC_No.", type text}, 
        {"AC_Name", type text}, 
        {"District", type text}, 
        {"Male", Int64.Type}, 
        {"Female", Int64.Type}, 
        {"Third_Gender", Int64.Type}, 
        {"Total", Int64.Type}
    })
in
    FinalTypes
```

## 2. Fact_Voters_SIR_(Dec2025)

**Purpose:** Dec 2025 voter snapshot (Year End).

### Key Steps

- Web import from TN Elections site.

- **Transformation:** Fixed inconsistent hyphenation in AC names.

- **Schema Alignment:** Renamed `AC No.` to `AC_No.` to match Jan 25.

- **Snapshot:** Added `Snapshot_Date = 2025-12-19`.

### M Code

```m
let
    // 1. Source & Filter
    Source = #"tbl_AC_GenderCount_Dec25-raw",
    RemoveTotals = Table.SelectRows(Source, each ([Name of Assembly Constituency] <> "Total")),

    // 2. District & Column Cleanup
    FillDistrict = Table.FillDown(RemoveTotals, {"District Name"}),
    RemoveCols = Table.RemoveColumns(FillDistrict, {"District No."}),

    // 3. Name Standardization
    FixNames = Table.TransformColumns(RemoveCols, {
        {"Name of Assembly Constituency", each 
            let 
                Step1 = Text.Replace(_, "Thiru-Vi-Ka-Nagar", "Thiru. Vi. Ka. Nagar (SC)"),
                Final = Text.Replace(Step1, "Chepauk Thiruvallikeni", "Chepauk-Thiruvallikeni") 
            in Final, 
            type text
        }
    }),

    // 4. Change Type
    ChangeType = Table.TransformColumnTypes(FixNames,{{"AC No.", type text}}),

    // 5. Add Date & Final Rename
    AddDate = Table.AddColumn(ChangeType, "Snapshot_Date", each #date(2025, 12, 19), type date),
    FinalSchema = Table.RenameColumns(AddDate, { 
        {"AC No.", "AC_No."}, 
        {"Third Gender", "Third_Gender"},
        {"Name of Assembly Constituency", "AC_Name"},
        {"District Name", "District"} 
    })
in
    FinalSchema
```

## 3. fact_ASD

**Purpose:** ASD Cleanup data for SIR 2026

### Key Steps

- **Data Typing:** Explicitly set numeric columns to `Int64` for accurate Sum aggregation.

- **Standardization:** Renamed `Shifted/ Absent` to `Shifted_Absent`.

- **Snapshot:** Added `Snapshot_Date = 2026-01-01`.

### M Code

```m
let
    Source = #"tbl_AC_ASD_Cleanup-raw",

    // 1. Filter Rows
    Filtered_Rows = Table.SelectRows(Source, each ([No and Name of the AC] <> "GRAND TOTAL" and [No and Name of the AC] <> "Total")),

    // 2. Split AC (To extract AC_No only)
    Split_AC = Table.SplitColumn(Filtered_Rows, "No and Name of the AC", 
        Splitter.SplitTextByCharacterTransition({"0".."9"}, (c) => not List.Contains({"0".."9"}, c)), {"AC_No.", "AC_Name_Ignore"}),

    // 3. Select & Rename (Dropping District, S.No, and Name here to save processing)
    Select_Columns = Table.SelectColumns(Split_AC, {"AC_No.", "Deceased", "Shifted/ Absent","Enrolled at multiple places", "Total"}),
    Renamed_Columns = Table.RenameColumns(Select_Columns, {
        {"Shifted/ Absent", "Shifted_Absent"}, 
        {"Enrolled at multiple places", "Enrolled_Multiple_Places"}
    }),

    // 4. Add Date & Final Types
    Added_Date = Table.AddColumn(Renamed_Columns, "Snapshot_Date", each #date(2026, 1, 1), type date),
    Final_Types = Table.TransformColumnTypes(Added_Date, {
        {"AC_No.", Int64.Type}, 
        {"Deceased", Int64.Type}, 
        {"Shifted_Absent", Int64.Type}, 
        {"Enrolled_Multiple_Places", Int64.Type}, 
        {"Total", Int64.Type}
    })
in
    Final_Types
```

## 4. dim_AC

**Purpose:** Assembly Constituency reference table

### Key Steps

- **Source:** Referenced from the cleaned `tbl_AC_GenderCount_Jan25`.

- **Dedup:** Removed duplicates to ensure a unique list of 234 ACs.

- **Usage:** Acts as the primary filter for the Star Schema.

### M Code

```m
let
    Source = #"tbl_AC_GenderCount_Jan25-raw",

    // 1. Filter Rows & Fill Down
    Filtered_Rows = Table.SelectRows(Source, each ([No. and Name of the AC] <> "Grand Total" and [No. and Name of the AC] <> "Total")),
    Filled_Down = Table.FillDown(Filtered_Rows,{"No. and Name of the District"}),

    // 2. Split District & Clean (Combine Split + Replace + Trim)
    Split_District = Table.SplitColumn(Filled_Down, "No. and Name of the District", 
        Splitter.SplitTextByCharacterTransition({"0".."9"}, (c) => not List.Contains({"0".."9"}, c)), {"Temp.Dist.No", "District"}),
    Cleaned_District = Table.TransformColumns(Split_District, {{"District", each Text.Trim(Text.Replace(_, "-", "")), type text}}),

    // 3. Split AC & Apply Complex Text Fixes
    Split_AC = Table.SplitColumn(Cleaned_District, "No. and Name of the AC", 
        Splitter.SplitTextByCharacterTransition({"0".."9"}, (c) => not List.Contains({"0".."9"}, c)), {"AC_No.", "AC_Name"}),
    
    Cleaned_AC_Name = Table.TransformColumns(Split_AC, {
        {"AC_Name", each 
            let 
                Step1 = Text.Replace(_, "-", " "),
                Step2 = Text.Replace(Step1, " Chepauk Thiruvallikeni", " Chepauk-Thiruvallikeni"),
                Step3 = Text.Replace(Step2, " Thiru Vi Ka Nagar (SC)", "Thiru.Vi.Ka. Nagar (SC)")
            in 
                Text.Trim(Step3), type text}
    }),

    // 4. Select Final Columns (Implicitly handles Reordering)
    Final_Output = Table.SelectColumns(Cleaned_AC_Name,{"AC_No.", "AC_Name", "District"})
in
    Final_Output
```

## 5. fact_Voters

**Purpose:** Merged voter snapshots for SSR and SIR

### Key Steps

- **Merge:** Combined `Fact_Voters_SSR_(Jan2025)` and `Fact
_Voters_SIR_(Dec2025)` using `AC_No.` as the key.
- **Append:** Stacked both snapshots vertically to create a unified fact table.

### M Code

```m
let
    // 1. Remove columns individually first (Lighter memory load)
    Table1 = Table.RemoveColumns(#"Fact_Voters_SSR_(Jan2025)", {"District", "AC_Name"}),
    Table2 = Table.RemoveColumns(#"Fact_Voters_SIR_(Dec2025)", {"District", "AC_Name"}),

    // 2. Combine & Remove Duplicates
    Source = Table.Combine({Table1, Table2}),
    Removed_Duplicates = Table.Distinct(Source)
in
    Removed_Duplicates
```