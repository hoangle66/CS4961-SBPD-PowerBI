# CS4961 SBPD Power BI

Power BI model and report for the Santa Barbara Public Defender HR & EPR dashboards.

This repo keeps two main `.pbix` files:

- `SBPD_HR_EPR_Model.pbix`  
- `SBPD_HR_EPR_Report.pbix`  

---

## 1. Model vs Report

### `SBPD_HR_EPR_Model.pbix` (Semantic Model)

- Connects to current V3.0 Smartsheet data.
- Contains all **Power Query** steps, calculated columns, and core **DAX tables/measures**.  
- Tables of interest:
  - `Employees`  (wired to James's current **Employees V3.0** Smartsheet)
  - `Old Employees`  (backup/test employee + EPR data)
  - Dimension tables such as `Dim_Employee`, `Dim_EPRStage`, `Dim_JobClass`, `Dim_Date`.

In short: This file is where I edit current data connections, queries, DAX, and test visuals 

---

### `SBPD_HR_EPR_Report.pbix`  (Thin Report)

- Connects to James's old Employee Smartsheet data.
- Visuals are more populated and fuller ready to be viewed for presenting.
- Uses a **live connection** to the published semantic model.
- Holds all the **visuals, pages, and formatting**, but no separate data refresh.  
- Safe place to:
  - Add or adjust visuals.
  - Edit titles, tooltips, colors, and layout.
- The report can be opened and edited without touching the data model.

In short: This file is where I adjusted the dashboard to be ready for ealier demos (no longer needed).

---

## 2. Employees V3.0 vs Old Employees

### `Employees` table  (current, live data)

- Source: **Smartsheet workspace `SBPD V3` → sheet `Employees V3.0`**.  
- Represents the **current employee roster**, including:
  - Status and DocuSign status  
  - Primary key column  
  - First and last name  
  - Job class, job class level, job class step  
  - Anniversary month and probation fields  
- This is the table that HR and Smartsheet are actively maintaining going forward.

### `Old Employees` table  (backup / sample data)

- James's copy of earlier employee table used when the project started.  
- Contains more complete **sample EPR dates and stages** than the current Smartsheet while the new sheet is still being built.  
- Some EPR visuals may still point at this table so that we can demonstrate the layout and logic while waiting for final V3.0 EPR fields.

Plan: once Employees V3.0 has full EPR data, we will migrate all visuals and measures away from `Old Employees` and retire it.

---

