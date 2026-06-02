# 🏥 Hospital Patient Analytics Dashboard
### Power BI · Power Query · DAX · ETL Pipeline

> **End-to-end healthcare analytics project** — raw, messy Kaggle data → transformed, modeled, and visualized into a recruiter-ready BI dashboard.

[![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![Dataset](https://img.shields.io/badge/Dataset-55%2C500%20Records-blue?style=for-the-badge)](https://www.kaggle.com/)
[![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)]()

---

## 📌 Project Overview

This project transforms a **55,500-record healthcare dataset** from Kaggle into a fully interactive Power BI dashboard. It covers the complete analytics workflow — from raw data ingestion to business-ready insights — focused on patient demographics, billing analysis, hospital performance, and insurance provider distribution.

| 🔢 Metric | Value |
|---|---|
| 📋 Total Records | 55,500 patients |
| 🏥 Medical Conditions | 6 (Cancer, Diabetes, Obesity, Asthma, Hypertension, Arthritis) |
| 🏦 Insurance Providers | 5 (Blue Cross, Aetna, Medicare, UnitedHealthcare, Cigna) |
| 📅 Admission Years | 2019 – 2024 |
| 💰 Total Billing Revenue | ₹1,41,42,32,400 |
| ⏱ Avg Length of Stay | 16 Days |

---

## 🗂️ Repository Structure

```
📁 Hospital-Patient-Analytics/
├── 📊 healthcare_dashboard.pbix        ← Power BI report file
├── 📄 healthcare_dataset.csv           ← Raw source data (Kaggle)
├── 📄 Condition_Dept_Lookup.csv        ← Reference/lookup table
├── 📁 screenshots/
│   ├── dashboard_main.png
│   ├── condition_summary.png
│   ├── insurance_summary.png
│   ├── patient_detail_table.png
│   └── billing_by_tier.png
└── 📝 README.md
```

---

## ⚙️ ETL Pipeline

The data was processed through a structured **Extract → Transform → Load** pipeline entirely inside **Power Query Editor** before modelling in Power BI.

```mermaid
flowchart LR
    A[("📥 EXTRACT\n──────────\nRaw CSV\n55,500 rows\n15 columns")] --> B

    subgraph B ["🔧 TRANSFORM — Power Query Editor"]
        direction TB
        B1[Fix Name Casing] --> B2[Parse Dates]
        B2 --> B3[Derive Age_Category]
        B3 --> B4[Calc Length_of_Stay]
        B4 --> B5[Add Billing_Tier]
        B5 --> B6[Merge Lookup Table]
        B6 --> B7[Validate & Remove Errors]
        B7 --> B8[(Group By\nCondition_Summary\nInsurance_Summary)]
    end

    B --> C[("📤 LOAD\n──────────\nPower BI\nData Model\n+ Dashboard")]
```

---

## 🔧 Power Query Transformations (Step-by-Step)

Every transformation was done in **Power Query Editor** — no manual editing of raw data.

### 1️⃣ Data Cleaning

| Step | Action | Reason |
|------|--------|--------|
| `Text.Proper()` | Fixed inconsistent name casing (e.g. `BobbY JaCKsoN` → `Bobby Jackson`) | Data standardization |
| Column rename | Renamed all columns to clean, snake_case format | Consistency across tables |
| Type assignment | Set correct data types for dates, decimals, integers, text | Prevent calculation errors |
| Date parsing | Parsed `Date of Admission` & `Discharge Date` as proper Date type | Required for LOS calculation |

### 2️⃣ Feature Engineering

```
Length_of_Stay  =  Discharge Date  −  Date of Admission   (in days)

Age_Category    =  if Age < 18  → "Minor"
                   if Age < 60  → "Adult"
                   else         → "Senior"

Billing_Tier    =  if Billing Amount < 10,000  → "Standard"
                   if Billing Amount < 20,000  → "Silver"
                   if Billing Amount < 35,000  → "Gold"
                   else                        → "Platinum"

Admission_Year  =  Date.Year([Date of Admission])
```

### 3️⃣ Lookup Table Merge

A separate `Condition_Dept_Lookup.csv` was imported and **merged (left join)** to map each Medical Condition to its hospital Department:

| Medical Condition | Department |
|---|---|
| Cancer | Oncology |
| Diabetes | Endocrinology |
| Hypertension | Cardiology |
| Obesity | Bariatrics |
| Asthma | Pulmonology |
| Arthritis | Rheumatology |

### 4️⃣ Summary Tables (Group By)

Two aggregated reference tables were created using **Group By** in Power Query:

**`Condition_Summary`** — grouped by Medical Condition:
- `Patient_Count`, `Avg_Billing`, `AVG_LOS`

**`Insurance_Summary`** — grouped by Insurance Provider:
- `Covered_Patient`, `Total_Claims`, `Avg_Claim`

---

## 📊 Dashboard Screenshots

### 🖥️ Main Dashboard
![Main Dashboard](Screenshots/Screenshot%202026-06-01%20195927.png)
> KPI cards for Total Patients, Total Billing, Avg Billing/Patient, Avg LOS · Top 10 Hospitals by Revenue · Monthly Admissions trend · Insurance distribution donut chart

---

### 🦠 Condition Summary Table (Power Query Output)
![Condition Summary](Screenshots/Screenshot%202026-06-01%20151003.png)
> Aggregated `Condition_Summary` table showing Patient Count, Avg Billing, and Avg Length of Stay per medical condition — built entirely via Power Query Group By.

---

### 🏦 Insurance Summary Table (Power Query Output)
![Insurance Summary](Screenshots/Screenshot%202026-06-01%20151033.png)
> `Insurance_Summary` table showing Covered Patients, Total Claims, and Avg Claim per insurance provider — 100% valid, 0% errors.

---

### 🧑‍⚕️ Patient Detail Table with Slicers
![Patient Table](Screenshots/Screenshot%202026-06-01%20195955.png)
> Drillable patient-level table filtered by Medical Condition and Admission Type — showing Age Category, Department, LOS, and Billing.

---

### 💰 Billing Tier by Department & Year
![Billing Tier](Screenshots/Screenshot%202026-06-01%20200025.png)
> Grouped bar chart showing Average Billing Amount segmented by Billing Tier (Standard / Silver / Gold / Platinum) across all 6 departments — filterable by Admission Year.

---

## 📐 Data Model

```mermaid
erDiagram
    healthcare_dataset {
        string Name
        int Age
        string Age_Category
        string Gender
        string Medical_Condition
        string Department
        date Date_of_Admission
        int Admission_Year
        date Discharge_Date
        int Length_of_Stay
        float Billing_Amount
        string Billing_Tier
        string Insurance_Provider
        string Hospital
        string Admission_Type
    }

    Condition_Dept_Lookup {
        string Medical_Condition PK
        string Department
    }

    Condition_Summary {
        string Medical_Condition
        int Patient_Count
        float Avg_Billing
        float AVG_LOS
    }

    Insurance_Summary {
        string Insurance_Provider
        int Covered_Patient
        float Total_Claims
        float Avg_Claim
    }

    healthcare_dataset ||--|| Condition_Dept_Lookup : "Merge on Medical_Condition"
    healthcare_dataset ||--o{ Condition_Summary : "Group By"
    healthcare_dataset ||--o{ Insurance_Summary : "Group By"
```

---

## 📈 Key Insights

- 🔴 **Diabetes & Arthritis** have the highest patient counts (~9.3K each)
- 💸 **Obesity** has the highest Avg Billing ($25,808) among all conditions
- ⏱️ **Asthma** patients have the longest average stay (15.7 days)
- 🏥 **Cigna** covers the most patients (11,249) with total claims ~$287M
- 📅 Monthly admissions are **consistent across years** with no major seasonal spikes (except a 2023 dip — visible in trend line)
- 💰 **Platinum billing tier** ($45K avg) is uniformly distributed across all departments

---

## 🛠️ Tools & Skills Used

| Tool | Usage |
|------|-------|
| ![Power BI](https://img.shields.io/badge/-Power%20BI-F2C811?logo=powerbi&logoColor=black) | Dashboard design, DAX measures, data modelling |
| ![Power Query](https://img.shields.io/badge/-Power%20Query-217346?logo=microsoft&logoColor=white) | ETL pipeline, all data transformations |
| ![Excel](https://img.shields.io/badge/-Excel-217346?logo=microsoftexcel&logoColor=white) | Initial data inspection |
| ![CSV](https://img.shields.io/badge/-CSV%20Data-lightgrey) | Raw data source + lookup table |

**Skills demonstrated:** ETL pipeline design · Data cleaning · Feature engineering · Lookup merges · Aggregation · KPI dashboarding · Slicer/filter UX · Business storytelling with data

---

## 📁 Dataset Source

- **Source:** [Kaggle — Healthcare Dataset](https://www.kaggle.com/datasets/prasad22/healthcare-dataset)
- **Records:** 55,500 patients
- **Original columns:** 15 (Name, Age, Gender, Blood Type, Medical Condition, Date of Admission, Doctor, Hospital, Insurance Provider, Billing Amount, Room Number, Admission Type, Discharge Date, Medication, Test Results)

---

## 👤 Author

**Harshal** · Computer Engineering Undergraduate · C.K. Pithavala College of Engineering & Technology (GTU)

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin)](https://linkedin.com/in/your-profile)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=for-the-badge&logo=github)](https://github.com/HarshalVora86)

---

> 💡 *This project is part of an ongoing Data Science & AI/ML learning path covering Python, SQL, Power BI, Machine Learning, and more.*
