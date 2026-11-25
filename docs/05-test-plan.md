# Test plan

Run on the published app (phone and browser). Fill in **Result** and the date; note any fix you made.

| # | Area | Steps | Expected | Result |
|---|---|---|---|---|
| 1 | Cascading pickers | New inspection → pick *Laboratories* | Building shows only Lab Building A and B | |
| 2 | Cascading pickers | Pick *Lab Building B* | Room shows only B101, B105, B120 | |
| 3 | Cascading reset | Change group to *Offices* after picking a room | Building and Room clear | |
| 4 | Required location | Save with no room | Error "Pick a group, building and room." Nothing saved | |
| 5 | Date rule | Inspection date tomorrow | Error, nothing saved | |
| 6 | High severity | Severity High, no corrective action | Error, nothing saved | |
| 7 | High severity | Severity High, due 10 days later | Error "must be due within 7 days" | |
| 8 | Due before inspection | Due date before inspection date | Error, nothing saved | |
| 9 | Happy path + photo | Valid Medium finding with 2 photos | Saved; appears at top of list; photos in Detail and in the SharePoint item | |
| 10 | Edit | Open a finding → Edit → change Status to In progress | Saved; list shows new status | |
| 11 | Close | Detail → Close finding | Status Closed; button hidden | |
| 12 | Search / filter | Search "Kitchen"; filter Status = Open | Only matching rows | |
| 13 | Overdue highlight | Open item with past due date | Due text in red | |
| 14 | Alert flow | Save a High finding | Email within 2 min with building, room, link | |
| 15 | Overdue flow | Run manually | Email table lists every non-closed item past due | |
| 16 | Edit existing location | Edit INS-0002 | Pickers show its saved group/building/room | |

## Issues found and fixed

| # | Problem | Cause | Fix |
|---|---|---|---|
| | | | |
