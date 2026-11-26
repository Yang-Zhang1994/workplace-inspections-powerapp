# Test plan and results

Run on 2026-11-25 against the published app (Power Apps Preview, browser) and the two live flows.
✅ passed · ⬜ not run yet.

| # | Area | Steps | Expected | Result |
|---|---|---|---|---|
| 1 | Cascading pickers | New inspection → open the group picker | Exactly the 4 groups, sorted | ✅ Field Operations, Greenhouses, Laboratories, Offices |
| 2 | Cascading pickers | Pick *Laboratories* | Building shows only Lab Building A and B | ✅ |
| 3 | Cascading pickers | Pick *Lab Building B* | Room shows only B101, B105, B120 | ✅ |
| 4 | Cascading reset | Change group to *Offices* after picking a room | Building and Room clear | ✅ |
| 5 | Required location | Save with Building/Room empty | "Pick an inspection group, building and room." Nothing saved | ✅ |
| 6 | Required severity | Save a complete form with no Severity | SharePoint required-field error shown through `Form1.OnFailure` | ✅ "Field(s) with problems: 'Severity'" |
| 7 | Happy path | Greenhouses → Greenhouse 1 → G1 Potting Area, finding, Medium → Save | Row created in SharePoint, Status defaults to Open | ✅ `INS-20261125-181427` in the list |
| 8 | Auto ID and date | New inspection | Title `INS-yyyymmdd-hhmmss`, date = today | ✅ (after fix 1–2 below) |
| 9 | Existing record | Select INS-0001 | Pickers show its saved group/building/room | ✅ |
| 10 | Overdue highlight | List with past-due open items | Due text red for INS-0002 and INS-0003 only | ✅ |
| 11 | Alert flow | Add one **High** and one **Low** test inspection | Exactly one run, for the High item; email received | ✅ 1 run, Succeeded |
| 12 | Overdue flow | Run manually | Email lists every open item past due, nothing else | ✅ table = INS-0002, INS-0003 |
| 13 | Date rule | Inspection date tomorrow | Error, nothing saved | ⬜ |
| 14 | High severity rules | High with no corrective action / due > 7 days | Error, nothing saved | ⬜ |
| 15 | Photo | Attach 2 photos on a phone | Saved as SharePoint attachments | ⬜ |
| 16 | Search | Search "Kitchen", "Open" | Only matching rows | ⬜ |

## Issues found and fixed

| # | Problem | Cause | Fix |
|---|---|---|---|
| 1 | A new inspection's header still showed the previously selected record's ID | Generated header label always read `RecordsGallery1.Selected.Title` | `If(newMode, "New inspection", RecordsGallery1.Selected.Title)` |
| 2 | Inspection date started empty | Date picker defaulted to the (blank) field value | `If(Form1.Mode = FormMode.New, Today(), Parent.Default)` (card unlocked) |
| 3 | Alert flow wouldn't save: "Condition has 0 top level properties" | The designer's Copilot added an empty Condition | Removed it; filtered on the trigger instead with a trigger condition, so the flow doesn't even run for non-High items |
| 4 | Power BI "Visualize the list" not offered | Not available for lists on a personal OneDrive site in this tenant | Reporting through the *Open findings by building* and *Overdue* list views |
