# Nurse Staffing, Overtime & Agency Cost Analysis

An end-to-end healthcare analytics project that identifies nurse staffing gaps by hospital and department, then measures how staffing status relates to overtime and agency-nurse use.

## Business questions

1. Which hospitals and departments have the largest nurse staffing gaps?
2. Does understaffing increase overtime and agency-nurse use?

## Project overview

Hospital staffing leaders need a clear view of where vacancies are concentrated and whether staffing shortages are increasing labor pressure. This project uses a Databricks medallion pipeline and a Tableau-ready Gold table.

The dataset contains monthly records for four hospitals and six departments across 2025. Each final record represents one `month + hospital_id + department_id` combination.

## Data pipeline

```text
Raw CSV files → Bronze tables → Silver cleaned tables → Gold analytics table → Tableau dashboard
```

| Layer  | Purpose                                          | Tables                                                                                |
| ------ | ------------------------------------------------ | ------------------------------------------------------------------------------------- |
| Bronze | Raw ingested source data                         | `bronze_nurse_staffing`, `bronze_monthly_labor_cost`, `bronze_monthly_patient_volume` |
| Silver | Cleaned, typed, standardized, and validated data | `silver_nurse_staffing`, `silver_monthly_labor_cost`, `silver_monthly_patient_volume` |
| Gold   | Business-ready table for analysis and Tableau    | `gold_staffing_tableau_master`                                                        |

## Data model

All three datasets join on:

```text
month + hospital_id + department_id
```

| Source                 | Examples of fields used                                                                 |
| ---------------------- | --------------------------------------------------------------------------------------- |
| Monthly nurse staffing | Budgeted FTE, filled FTE, open positions, overtime hours, agency hours, staffing status |
| Monthly labor cost     | Regular pay, overtime pay, agency pay, benefits, paid hours                             |
| Monthly patient volume | Encounters, patient days, procedures, average wait time                                 |

## Key metrics

| Metric                   | Definition                                              |
| ------------------------ | ------------------------------------------------------- |
| Nurse staffing gap       | `budgeted_nurse_fte - filled_nurse_fte`                 |
| Vacancy rate             | `nurse_staffing_gap / budgeted_nurse_fte`               |
| Total labor cost         | Regular pay + overtime pay + agency pay + benefits cost |
| Overtime pay share       | Overtime pay / total labor cost                         |
| Agency pay share         | Agency pay / total labor cost                           |
| Encounters per paid hour | Patient encounters / paid hours                         |
| Labor cost per encounter | Total labor cost / patient encounters                   |

## Data quality decisions

- Standardized `hospital_id`, `department_id`, and `staffing_status` using `TRIM` and `UPPER`.
- Cast dates, FTE values, hours, and currency fields to appropriate data types.
- Removed one duplicate patient-volume record so every Silver table contains **288 records**.
- Retained a negative `agency_pay` value because it represents a valid payroll credit or adjustment.
- Validated that the Gold table has **288 records** and no duplicate join keys.

## Repository structure

```text
healthcare-staffing-analysis/
├── data/
│   ├── monthly_nurse_staffing.csv
│   ├── monthly_labor_cost.csv
│   ├── monthly_patient_volume.csv
│   └── gold_staffing_tableau_master.csv
├── notebooks/
│   ├── 01_bronze_ingestion.ipynb
│   ├── 02_silver_cleaning.ipynb
│   ├── 03_gold_staffing_analysis.ipynb
│   └── 04_business_questions.ipynb
├── dashboard/
│   ├── healthcare_staffing_dashboard.twbx
│   └── screenshots/
│       └── staffing_status_month.png
├── .gitignore
└── README.md
```

## How to run in Databricks

1. Upload the source CSV files to a Databricks Volume or workspace folder.
2. Create a schema
3. In each notebook, enter your own catalog name:

sql
USE CATALOG <your_catalog_name>;
USE SCHEMA <yourschema>;

4. Run the notebooks in order: Bronze → Silver → Gold → Business Questions.
5. Connect Tableau to `gold_staffing_tableau_master`, or export the table as a CSV for Tableau Public.

## Tableau dashboard

The dashboard includes:

- KPI cards for average staffing gap, vacancy rate, overtime hours, and agency hours
- Average staffing gap by hospital
- Overtime pay versus agency pay by hospital
- Hospital × department heatmap of average staffing gap
- Monthly staffing-status distribution by hospital
- Top 10 hospital-department staffing gaps

### Dashboard preview

![Healthcare Staffing Dashboard](dashboard/screenshots/staffing_status_month.png)

**Tableau Public dashboard:** [View the interactive dashboard](PASTE_YOUR_TABLEAU_PUBLIC_LINK_HERE)

## Findings to validate

The analysis identifies departments with the largest staffing gaps and tests whether understaffed departments use more overtime and agency coverage. These findings can help leaders prioritize staffing reviews, vacancy reduction, and agency-spend monitoring.

## Tools used

- Databricks SQL
- Delta Lake and medallion architecture
- Tableau Public
- GitHub




```

