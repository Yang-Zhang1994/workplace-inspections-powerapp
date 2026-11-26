# Step 4 — Reporting (about 20 min)

> **As built (2026-11-25).** *Integrate → Power BI* isn't offered for lists on a personal OneDrive site in
> this tenant, so reporting uses the list views below and the colour-coded Severity column.

## Power BI quick report (works in the browser, no Power BI Desktop needed)

1. Open the **Inspections** list → **Integrate → Power BI → Visualize the list**.
2. Power BI builds a starter report. Switch to **Edit** and keep three visuals:
   - **Card:** count of `Title` filtered to `Status` ≠ Closed → *Open findings*.
   - **Stacked bar:** axis `Building`, legend `Severity`, value count of `Title`.
   - **Table:** `Title`, `Building`, `Room`, `DueDate`, `Status`, filtered to Status ≠ Closed, sorted by DueDate.
3. **Publish to the list**, name it *Inspection overview*.

If Power BI asks for a licence you can't get, use the fallback:

## Fallback: list views and Excel

- The **Open findings by building** and **Overdue** views from step 1 already work as live reports.
- **Export → Export to Excel (.iqy / CSV)**, insert a PivotTable (rows Building, columns Severity, values Count of Title) and a PivotChart.

**Screenshot:** the report → `screenshots/07-report.png`.
