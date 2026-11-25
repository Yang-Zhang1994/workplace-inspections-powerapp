# Step 1 — SharePoint lists (about 30 min)

The app stores everything in two Microsoft Lists, which live on SharePoint Online.

| List | Purpose | Rows |
|---|---|---|
| **Locations** | Every inspectable room: inspection group → building → room. Drives the cascading pickers. | 23 (`data/locations.csv`) |
| **Inspections** | One row per inspection finding, with photos as attachments. | 4 samples (`data/inspections-sample.csv`) |

## 1. Create the Locations list

1. Open **Microsoft Lists**: <https://www.microsoft365.com/lists> (signed in with your Northeastern account).
2. **+ New list → From CSV**, choose `data/locations.csv`.
3. Check the column types Lists suggests: all four are **Single line of text**. Name the list **Locations**, save it to **My lists**.

## 2. Create the Inspections list

1. **+ New list → From CSV**, choose `data/inspections-sample.csv`. Name it **Inspections**.
2. Set the column types on the import screen (or later in *List settings → column → Edit*):

| Column | Type | Settings |
|---|---|---|
| Title | Single line of text | Inspection ID, e.g. `INS-0001` |
| InspectionGroup, Building, Room | Single line of text | Required |
| InspectionDate | Date (no time) | Required |
| Finding | Multiple lines of text | Required |
| Severity | **Choice** | `Low`, `Medium`, `High`; required |
| HazardType | **Choice** | `Chemical storage`, `Electrical`, `Fire safety`, `Spill response`, `Trip hazard`, `PPE`, `Other`; allow fill-in |
| CorrectiveAction | Multiple lines of text | |
| DueDate | Date (no time) | |
| Status | **Choice** | `Open`, `In progress`, `Closed`; default `Open` |

3. Add one more column: **+ Add column → Person → Inspector** (optional, allow one person).
4. Attachments are on by default (*List settings → Advanced settings → Attachments: Enabled*). Photos are stored there.

## 3. Colour-code Severity (column formatting)

*Severity column → Column settings → Format this column → Advanced mode*, paste
[`formatting/severity-column.json`](../formatting/severity-column.json), **Save**.
High shows red, Medium amber, Low green.

## 4. Add two views

- **Open findings by building:** *+ Create view → List*, filter `Status is not equal to Closed`,
  group by `Building`, sort by `DueDate` ascending.
- **Overdue:** filter `Status is not equal to Closed` **and** `DueDate is less than [Today]`.

## 5. Note the site address

Open the Inspections list and copy the address up to the list name, e.g.
`https://northeastern-my.sharepoint.com/personal/zhang_yang15_northeastern_edu`.
Power Apps and Power Automate ask for this **site address**.

**Screenshot for the README:** the Inspections list with the coloured Severity column → `screenshots/01-list.png`.
