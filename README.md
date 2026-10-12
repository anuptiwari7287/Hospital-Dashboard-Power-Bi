# 🏥 Hospital Emergency Room Dashboard (Power BI)

An interactive **Power BI** dashboard that analyses emergency-room (ER) patient visits. It tracks patient volume, admission rates, patient demographics, department referrals and patient satisfaction, with year and month filters.

---

## 📌 Project Overview

| | |
|---|---|
| **File** | `hospital_dashboard.pbix` |
| **Tool** | Microsoft Power BI Desktop (Power Query, DAX, report visuals) |
| **Source** | `Hospital ER_Data.csv`: 9,216 ER visits × 12 columns |
| **Period** | 1 Apr 2023 – 30 Oct 2024 (19 months) |

**Goal:** give ER and hospital administrators a one-page view of *how many patients come in, who they are, where they are referred, how many are admitted, and how satisfied they are*.

---

## 🧱 Data Model

| Table | Description |
|---|---|
| **Hospital ER_Data** | Fact table with one row per ER visit |
| **Date Table** | Calendar table (`Date`, `Month Name`, `Year`) for time filtering and the monthly view |

### Source fields

| Column | Description |
|---|---|
| Patient Id | Unique visit/patient identifier |
| Patient Admission Date | Date and time of arrival |
| Patient First Inital / Last Name | Patient name |
| Patient Gender | Male, Female, Not Confirmed |
| Patient Age | Age in years (1–79) |
| Patient Race | 7 race categories |
| Department Referral | Department the patient was referred to (or None) |
| Patient Admission Flag | `True` if the patient was admitted to the hospital |
| Patient Satisfaction Score | Score from 0 to 10 (many visits have no score) |
| Patient Waittime | Wait time in minutes |
| Patients CM | Patient count helper column (integer) |

### Power Query transformations

- Promoted headers and set data types (text, datetime, whole number, true/false)
- Replaced gender codes: `M` → Male, `F` → Female, `NC` → Not Confirmed
- Added `Patient Full Name` = first initial + last name

### DAX

| Name | Type | Definition |
|---|---|---|
| `No of patients` | Measure | `DISTINCTCOUNT('Hospital ER_Data'[Patient Id])` |
| `Avg Patient Age` | Measure | `AVERAGE('Hospital ER_Data'[Patient Age])` |
| `Admitted Patients` | Measure | `CALCULATE(COUNTROWS('Hospital ER_Data'), [Patient Admission Flag] = TRUE())` |
| `Not Admited Patient` | Measure | Same as above with `FALSE()` |
| `Average Satisfaction Score` | Measure | `AVERAGE('Hospital ER_Data'[Patient Satisfaction Score])` |
| `Patient admin date` | Calculated column | Date-only version of the admission date |
| `Month Name`, `Year` | Calculated columns (Date Table) | `FORMAT(Date,"mmm")`, `YEAR(Date)` |

---

## 📊 Dashboard Contents

**KPI cards and gauges**

- **No. of patients**
- **Total Admitted Patients** (card and gauge)
- **Avg Patient Age** (card and gauge)
- **Avg Satisfaction Score** (card and gauge)

**Charts**

| Visual | Shows |
|---|---|
| Pie chart | Admitted vs not admitted patients |
| Donut chart | Admitted patients by gender |
| Clustered bar chart | Number of patients by department referral |
| Clustered bar chart | Number of patients by patient race |
| Stacked area chart | Number of patients over time (monthly view) |

**Filters (slicers):** Year · Month

---

## 🔍 Key Insights

*(Calculated from the data in the model.)*

- **9,216 patient visits** over 19 months, averaging **~485 visits a month**. Volume is steady: monthly counts range from 431 (Feb 2024) to 530 (Aug 2024).
- **Admission rate is ~50 %**: 4,612 admitted and 4,604 not admitted.
- **Average patient age is ~39.9 years.**
- **Average satisfaction score is ~5.0 out of 10**, but only **2,517 of 9,216 visits (~27 %)** have a score, so treat this figure with caution.
- **Average wait time is ~35 minutes** (not shown on the dashboard).
- **Most visits (~59 %) have no department referral.** Of the referred patients, **General Practice** (1,840) and **Orthopedics** (995) receive the most, and **Renal** the fewest (86).
- **Gender split of visits** is almost even: Male 4,705, Female 4,487, Not Confirmed 24.
- **Largest race groups:** White (2,571), African American (1,951) and Two or More Races (1,557).

---

## ▶️ How to Use

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free, Windows).
2. Open `hospital_dashboard.pbix`.
3. If Power BI shows a data-source error, go to **Home → Transform data → Data source settings → Change Source** and point it to your local `Hospital ER_Data.csv`. The path is currently hard-coded to the author's machine.
4. Click **Refresh**, then use the **Year** and **Month** slicers to filter all visuals. Hold `Ctrl` to select multiple values.

---

## ⚠️ Notes & Limitations

- **Satisfaction score is mostly blank** (~73 % of visits), so the average is based on a small sample.
- `Patient Waittime` is loaded but not used in any visual.
- The data looks **synthetic**: admission rates, volumes and age are nearly uniform across groups.
- ⚠️ **Privacy:** the `Patient Id` values look like Social Security numbers (format `XXX-XX-XXXX`). Do **not** upload the raw CSV to a public repo unless the IDs are anonymised or confirmed to be dummy data.

---

## 🚀 Possible Improvements

- Add **wait-time** analysis (average wait by department, hour of day or weekday)
- Add a **drill-through** page for individual departments
- Add an **age-group** breakdown and a **satisfaction-by-department** view
- Add measures for month-over-month change and admission rate %
- Replace the free-text gender and race columns with lookup dimensions

