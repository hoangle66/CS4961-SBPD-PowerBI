# CS4961 SBPD Power BI

Power BI model and report for the Santa Barbara Public Defender HR & EPR dashboards.

This repo keeps two main `.pbix` files:

- `SBPD_HR_EPR_Model.pbix`  
- `SBPD_HR_EPR_Report.pbix`  

---

## 1. Model vs Report

### `SBPD_HR_EPR_Model.pbix`  (Semantic Model)

- Connects to Smartsheet and other sources.  
- Contains all **Power Query** steps, calculated columns, and core **DAX tables/measures**.  
- Tables of interest:
  - `Employees`  (wired to James's current **Employees V3.0** Smartsheet)
  - `Old Employees`  (backup/test employee + EPR data)
  - Dimension tables such as `Dim_Employee`, `Dim_EPRStage`, `Dim_JobClass`, `Dim_Date`.

In short: This file is where I edit current data connections, queries, DAX, and test visuals.

---

### `SBPD_HR_EPR_Report.pbix`  (Thin Report)

- Uses a **live connection** to the published semantic model.  
- Holds all the **visuals, pages, and formatting**, but no separate data refresh.  
- Safe place to:
  - Add or adjust visuals.
  - Edit titles, tooltips, colors, and layout.
- The report can be opened and edited without touching the data model.

In short: This file is where I adjust the dashboard to be ready for demo.

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

## 3. HR Overview page (Report) - Which is still currently connected to the old employee Smartsheet data for fuller visuals.

**Purpose:** quick read on the current workforce structure.

Key pieces:

- **Cards at the top**
  - `Job Class Count` – number of distinct job classes in the roster.  
  - `Employees Count` – total active employees.  
  - `Supervisors Count` – number of employees marked as supervisors.  
  - `Interns` – count of interns.  
  - `Employees on Probation` – count of employees currently flagged as probationary.

- **Find Employee slicer**
  - Uses `Dim_Employee[EmployeeFullName]`.  
  - Type a name and check the box to focus the entire page on one or more employees.  
  - Clearing the selection returns the page to “everyone”.

- **Employee Directory table**
  - Shows `FullName`, `Job Class`, `Job Class Level`, and `Supervisor`.  
  - Driven directly from the `Employees` Smartsheet data.

- **Employees’ Work Anniversary by Month**
  - Column chart of employee counts by `Anniversary Month`.  
  - Helps HR see which months are “heavy” for evaluations and recognitions.

- **Supervisors’ Team table**
  - Each row is a supervisor.  
  - Columns show how many employees, interns, and employees on probation they currently manage.

- **Employees on Probation by Job Class Level**
  - Donut chart that breaks down probationary employees by job class level.

---

## 4. EPR Pages – What the visuals and slicers mean

The EPR pages use James’s EPR date fields. While Smartsheet V3.0 is still being filled, some visuals may still rely on `Old Employees` for complete sample data.

### Signed EPR Due Date slicer (Current Cycle)

- Slicer field: `Signed EPR Due Date` for the **current** EPR cycle.  
- The slider lets HR filter the EPR visuals to a particular due date range (for example, one fiscal year or one review window).  
- This slicer is about the **current cycle’s due dates**, not previous cycles.

### EPR Summary cards

At the top of the EPR page:

- `Total EPRs` – count of current cycle EPR records.  
- `Completed EPRs` – count where the current cycle EPR is finished.  
- `Incomplete EPRs` – everything not completed.  
- `Total Late EPRs` – how many EPRs are marked late based on the late status logic.  
- `Probationary EPRs (Incomplete)` – incomplete EPRs for employees currently on probation.

These cards respond to the Signed EPR Due Date slicer and other filters.

### Completed vs Incomplete donut

- Splits the current cycle EPRs into **Completed** vs **Incomplete**.  
- Uses the same late-status fields as the cards.

### Incomplete EPRs by Supervisor – Late vs On Time

- Bar chart where each row is a **supervisor**.  
- Bars are split by **Late** vs **On Time** for incomplete EPRs, so HR can see where follow-up is needed.

### Number of EPRs in Each Stage

- Table that uses `Dim_EPRStage` and the `Employees[Status]` field.  
- Stages roughly represent who “owns” the EPR at the moment:

  - Not Created  
  - With Supervisor 1 / 2 / 3  
  - With HR  
  - With Head of HR  
  - With Head of Office  
  - Completed  

- `Total EPRs in Stage` counts how many current cycle EPRs are sitting at each step.  
- The `StageOwner` attribute is used in text and tooltips to explain who is responsible at each stage.

### EPRs Timeliness (Previous Cycle)

- Table that looks at the **previous EPR cycle** using:

  - `Previous EPR Signed` – when the last cycle was actually signed.  
  - `Previous EPR Actual Due Date` – the scheduled due date for that previous cycle.  
  - `Days to Due (Prev Cycle)` – DAX measure based on the difference between previous signed date and previous due date.  
  - `Late Status` – label such as **On Time** or **Late** based on the days difference.

- This table does **not** respond to the Signed EPR Due Date slicer, because it is explicitly about the previous cycle. It is intended for historical context, not current filtering.

### Average Days Late by Due Month (Completed EPRs)

- Uses previous cycle **completed** EPRs.  
- Aggregates `Days to Due (Prev Cycle)` by month of due date.  
- Helps show which months tended to be earlier or later relative to their due dates.

---

## 5. Current status and next steps

- The **HR page** in the Report file (wired to old Employee Smartsheet) is really similar with the Model file that is fully wired to `Employees V3.0`.  
- On the **EPR pages** for the report file:
  - The structure, measures, and visuals are in place.  
  - Some visuals still rely on `Old Employees` for sample EPR dates while the new Smartsheet fields are being populated.  
- Once Smartsheet V3.0 includes final EPR date fields, we will:
  - Point all EPR measures to the live `Employees` table.  
  - Remove the `Old Employees` table from the model.  
  - Re-test the slicers and cards against real production data.

If anything in this layout or naming does not match how we want to think about “current vs previous cycle,” please leave comments or open an issue on this repo so we can adjust the labels and logic.
