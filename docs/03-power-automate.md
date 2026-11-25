# Step 3 — Power Automate flows (about 30 min)

<https://make.powerautomate.com> → **+ Create**.

## Flow 1: alert on a high-severity finding

**Automated cloud flow**, name *Inspection – high-severity alert*.

1. Trigger **SharePoint → When an item is created or modified**: Site address = your site, List = `Inspections`.
2. **Condition** (Advanced mode):
   ```
   @and(equals(triggerOutputs()?['body/Severity/Value'], 'High'), not(equals(triggerOutputs()?['body/Status/Value'], 'Closed')))
   ```
3. **If yes → Office 365 Outlook → Send an email (V2)**
   - To: your Northeastern email
   - Subject: `High-severity finding: @{triggerOutputs()?['body/Building']} – @{triggerOutputs()?['body/Room']}`
   - Body:
     ```
     Finding: @{triggerOutputs()?['body/Finding']}
     Corrective action: @{triggerOutputs()?['body/CorrectiveAction']}
     Due: @{triggerOutputs()?['body/DueDate']}
     Open the item: @{triggerOutputs()?['body/{Link}']}
     ```
4. *Settings* on the trigger → **Trigger conditions** → add
   `@equals(triggerOutputs()?['body/Severity/Value'], 'High')` so the flow doesn't run (or email) on every edit of a low finding.

## Flow 2: daily overdue reminder

**Scheduled cloud flow**, name *Inspection – overdue reminder*, every **1 day** at **08:00**, time zone Pacific.

1. **SharePoint → Get items**: List = `Inspections`, Filter Query:
   ```
   Status ne 'Closed' and DueDate lt '@{formatDateTime(utcNow(), 'yyyy-MM-dd')}'
   ```
2. **Condition**: `length(body('Get_items')?['value'])` is greater than `0`.
3. **If yes → Data operation → Select** from `value`, map
   `ID` → `Title`, `Where` → `concat(item()?['Building'], ' – ', item()?['Room'])`, `Due` → `item()?['DueDate']`, `Status` → `item()?['Status/Value']`.
4. **Create HTML table** from the Select output → **Send an email (V2)** with subject
   `@{length(body('Get_items')?['value'])} overdue inspection findings` and the table as body.

## Test both

- Save a **High** finding in the app → the alert email arrives within a minute or two.
- Run Flow 2 manually (**Test → Manually**) → the sample `INS-0003` (due Nov 19, Open) is listed.

**Screenshots:** each flow's run history showing *Succeeded* → `screenshots/05-flow-alert.png`, `screenshots/06-flow-overdue.png`.
