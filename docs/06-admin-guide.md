# Admin guide

## Components

| Component | Where | Owner task |
|---|---|---|
| `Locations` list | SharePoint (My lists) | Add, rename or retire rooms |
| `Inspections` list | SharePoint | Holds every finding and its photos |
| *Workplace Inspections* canvas app | Power Apps, default environment | Publish changes, share |
| *High-severity alert* flow | Power Automate | Keep the recipient current |
| *Overdue reminder* flow | Power Automate | Runs daily |
| *Open findings by building*, *Overdue* | List views | Live; no refresh needed |

## Common tasks

- **Add a room:** add a row to `Locations` (Title `LOC-nnn`, group, building, room). It appears in the app's pickers on next launch; no app change.
- **Retire a room:** delete its `Locations` row. Past inspections keep their text values.
- **Give someone access:** share the app (*Apps → … → Share*) **and** give them *Edit* on both lists — the app uses the user's own SharePoint permissions.
- **Change the high-severity rule:** the 7-day rule is in `btnSave.OnSelect` on FormScreen; the alert condition is in Flow 1.

## Limits and known trade-offs

- `Distinct` and `in` are not delegable to SharePoint. With the data row limit at 2,000 they stay correct until a list passes 2,000 rows; beyond that, switch search to `StartsWith` and add an indexed `Status` column.
- Location values are copied into each inspection as text, so renaming a room doesn't rewrite history (intended for audit).
- Photos count against the site's storage; each item allows 5 attachments of up to 10 MB.
