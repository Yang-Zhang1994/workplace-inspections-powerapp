# Step 2 — The canvas app (about 2 hours)

Three screens: a list of inspections, a form to record or edit one, and a detail view.

```
HomeScreen ──New──► FormScreen ──Save──► HomeScreen
    │                    ▲
    └──select──► DetailScreen ──Edit──┘     (Close finding: one tap)
```

## 0. Create the app and connect the data

1. <https://make.powerapps.com> → **+ Create → Start with a blank canvas → Phone size** (inspectors walk around with a phone). Name it **Workplace Inspections**.
2. Left bar **Data → Add data → SharePoint**, sign in, paste the **site address** from step 1, tick **Locations** and **Inspections**.
3. **Settings → General → Data row limit**: set to **2000** (Distinct/`in` below are not delegable; this keeps them correct for lists up to 2,000 rows — enough for a site's rooms).

Rename every control exactly as below (double-click it in the Tree view) — the formulas refer to these names.

## 1. HomeScreen

| Control | Property | Formula |
|---|---|---|
| `txtSearch` (Text input) | HintText | `"Search building, room or finding"` |
| `drpStatus` (Drop down) | Items | `["All", "Open", "In progress", "Closed"]` |
| `galInspections` (Vertical gallery) | Items | see below |
| `btnNew` (Button, text "New inspection") | OnSelect | `NewForm(frmInspection); Navigate(FormScreen, ScreenTransition.Cover)` |

`galInspections.Items`:

```powerfx
SortByColumns(
    Filter(
        Inspections,
        drpStatus.Selected.Value = "All" || Status.Value = drpStatus.Selected.Value,
        IsBlank(txtSearch.Text)
            || txtSearch.Text in Building
            || txtSearch.Text in Room
            || txtSearch.Text in Finding
    ),
    "InspectionDate",
    SortOrder.Descending
)
```

Inside the gallery template:

| Control | Property | Formula |
|---|---|---|
| `lblWhere` | Text | `ThisItem.Building & " · " & ThisItem.Room` |
| `lblFinding` | Text | `ThisItem.Finding` |
| `lblDue` | Text | `"Due " & Text(ThisItem.DueDate, "mmm d") & " · " & ThisItem.Status.Value` |
| `rectSeverity` (thin rectangle on the left) | Fill | `Switch(ThisItem.Severity.Value, "High", RGBA(196,49,75,1), "Medium", RGBA(202,122,0,1), RGBA(46,125,50,1))` |
| `lblDue` | Color | `If(ThisItem.Status.Value <> "Closed" && ThisItem.DueDate < Today(), Color.Red, Color.Black)` |
| gallery | OnSelect | `Navigate(DetailScreen, ScreenTransition.Fade)` |

## 2. FormScreen

1. Insert **Edit form** `frmInspection`, DataSource `Inspections`, Item `galInspections.Selected`.
2. **Edit fields**: keep Title, InspectionDate, Finding, Severity, HazardType, CorrectiveAction, DueDate, Status, Inspector, **Attachments**; add InspectionGroup, Building, Room.
3. Rename the generated inputs: `dpInspectionDate`, `cbSeverity`, `txtCorrectiveAction`, `dpDueDate`.
4. **Title card** → unlock → `Default`:

```powerfx
If(frmInspection.Mode = FormMode.New, "INS-" & Text(Now(), "yyyymmdd-hhmmss"), ThisItem.Title)
```

### Cascading pickers (group → building → room)

In each of the three cards (InspectionGroup, Building, Room): unlock, delete the text box, insert a **Drop down**.

| Control | Property | Formula |
|---|---|---|
| `ddGroup` | Items | `Sort(Distinct(Locations, InspectionGroup), Value)` |
| `ddGroup` | Default | `Parent.Default` |
| `ddGroup` | OnChange | `Reset(ddBuilding); Reset(ddRoom)` |
| `ddBuilding` | Items | `Sort(Distinct(Filter(Locations, InspectionGroup = ddGroup.Selected.Value), Building), Value)` |
| `ddBuilding` | Default | `Parent.Default` |
| `ddBuilding` | OnChange | `Reset(ddRoom)` |
| `ddRoom` | Items | `Sort(Distinct(Filter(Locations, InspectionGroup = ddGroup.Selected.Value, Building = ddBuilding.Selected.Value), Room), Value)` |
| `ddRoom` | Default | `Parent.Default` |

Set each card's **Update** to its dropdown: `ddGroup.Selected.Value`, `ddBuilding.Selected.Value`, `ddRoom.Selected.Value`.

### Photos

The **Attachments** card is the photo field: on a phone, *Attach file* offers the camera. Set the card's
`MaxAttachments` to `5` and `MaxAttachmentSize` to `10` (MB).

### Validation and saving

`btnSave` (Button "Save") → OnSelect:

```powerfx
If(
    IsBlank(ddRoom.Selected.Value),
        Notify("Pick a group, building and room.", NotificationType.Error),
    IsBlank(dpInspectionDate.SelectedDate) || dpInspectionDate.SelectedDate > Today(),
        Notify("Inspection date is required and can't be in the future.", NotificationType.Error),
    cbSeverity.Selected.Value = "High" && IsBlank(txtCorrectiveAction.Text),
        Notify("A high-severity finding needs a corrective action.", NotificationType.Error),
    cbSeverity.Selected.Value = "High"
        && (IsBlank(dpDueDate.SelectedDate)
            || dpDueDate.SelectedDate > DateAdd(dpInspectionDate.SelectedDate, 7, TimeUnit.Days)),
        Notify("A high-severity finding must be due within 7 days of the inspection.", NotificationType.Error),
    !IsBlank(dpDueDate.SelectedDate) && dpDueDate.SelectedDate < dpInspectionDate.SelectedDate,
        Notify("Due date can't be before the inspection date.", NotificationType.Error),
    SubmitForm(frmInspection)
)
```

| Control | Property | Formula |
|---|---|---|
| `frmInspection` | OnSuccess | `Notify("Inspection saved.", NotificationType.Success); Navigate(HomeScreen, ScreenTransition.UnCover)` |
| `frmInspection` | OnFailure | `Notify("Couldn't save: " & frmInspection.Error, NotificationType.Error)` |
| `btnCancel` | OnSelect | `ResetForm(frmInspection); Back()` |

## 3. DetailScreen

1. Insert **Display form** `frmDetail`, DataSource `Inspections`, Item `galInspections.Selected`, include **Attachments** (shows the photos).
2. Buttons:

| Control | Property | Formula |
|---|---|---|
| `btnEdit` | OnSelect | `EditForm(frmInspection); Navigate(FormScreen)` |
| `btnClose` (text "Close finding") | OnSelect | `Patch(Inspections, galInspections.Selected, {Status: {Value: "Closed"}}); Notify("Finding closed.", NotificationType.Success); Back()` |
| `btnClose` | Visible | `galInspections.Selected.Status.Value <> "Closed"` |
| `btnBack` | OnSelect | `Back()` |

## 4. Save, publish, test on your phone

**File → Save → Publish**. Install the **Power Apps** mobile app, sign in, open *Workplace Inspections*,
and record one real inspection with a photo. Then run the test plan in [`05-test-plan.md`](05-test-plan.md).

**Screenshots:** HomeScreen → `screenshots/02-home.png`, FormScreen with the cascading pickers open → `screenshots/03-form.png`,
a validation message → `screenshots/04-validation.png`.
