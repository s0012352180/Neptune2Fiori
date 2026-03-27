# UI Component: footerPageCCreate
**App:** ZDCI_NAD_PROJECT_REQUEST
**Type:** sap.m.Bar
**Position:** setFooter (page footer bar)
**Purpose:** Footer action bar on the Create/Edit form page — contains all action buttons.

---

## Button Summary

| Button Name | Text | Type | Icon | Visible (Binding) | Press Action |
|-------------|------|------|------|-------------------|--------------|
| butSave | Save | Transparent | sap-icon:save | {GS_PROJ_FORM-SAVE} | Validate ITL radio → call `getOnlineSaveAsDraft()` |
| butDelete | Delete - saved draft | Transparent | sap-icon:delete | {GS_PROJ_FORM-DELETE} | `oApp.setBusy(true)` → `diaDelDraft.open()` |
| butCreate | Submit Project Code Request | Accept | sap-icon:Checasse | {GS_PROJ_FORM-CREATE} | Run validation chain → `diaCreate.open()` |
| butChange | Change Project Code Request | Accept | — | {GS_PROJ_FORM-CHANGE} | `validateRequiredAll()` → `getOnlineCheck()` |
| butSubmitWthErr | Submit With Error | Emphasized | — | {GS_PROJ_FORM-SUBMITWITH_ERR} | `validateRequiredAll()` → `diaSubmitWthErr.open()` |
| butSubmit | Submit | Accept | — | {GS_PROJ_FORM-SUBMIT} | `validateRequiredAll()` → `getOnlineCheck()` |
| butReject | Reject | — | — | {GS_PROJ_FORM-REJECT} | `diaReject.open()` |
| butClose | Withdraw | Transparent | — | {GS_PROJ_FORM-WITHDRAWN} | `diaClose.open()` |
| butResubmit | Resubmit | Accept | — | {GS_PROJ_FORM-RESUBMIT} | Run validation chain → `diaReSubmit.open()` |

---

## Button Press Code

### butSave
```js
// oApp.setBusy(true); -- commented out busy indicator
oApp.setBusy(true);
setTimeout(function() {
    if (rbITLYes.getSelected()) {
        modeloPageProj.oData.ITL = "Y";
        modeloPageProj.refresh();
    } else {
        modeloPageProj.oData.ITL = "N";
        modeloPageProj.refresh();
    }
    getOnlineSaveAsDraft();
}, 200);
```

### butDelete
```js
oApp.setBusy(true);
diaDelDraft.open();
```

### butCreate
```js
if (!validateRequiredAll()) {
    if (!clear_value()) {
        if (!validate_projname()) {
            if (!validate_bussjustname()) {
                if (!validate_date()) {
                    if (!validateAmount()) {
                        if (!validateAttachment()) {
                            diaCreate.open();
                        }
                    }
                }
            }
        }
    }
}
```

### butChange
```js
if (!validateRequiredAll()) {
    getOnlineCheck();
    // diaChange.open(); -- commented out
}
```

### butSubmitWthErr
```js
if (!validateRequiredAll()) {
    diaSubmitWthErr.open();
}
```

### butSubmit
```js
if (!validateRequiredAll()) {
    getOnlineCheck();
}
```

### butReject
```js
diaReject.open();
```

### butClose
```js
diaClose.open();
```

### butResubmit
```js
if (!validateRequiredAll()) {
    if (!validateAmount()) {
        if (!validateAttachment()) {
            diaReSubmit.open();
        }
    }
}
```

---

## Validation Chain Reference

| Validation Function | Used By | What it checks |
|--------------------|---------|----------------|
| `validateRequiredAll()` | butCreate, butChange, butSubmitWthErr, butSubmit, butResubmit | All required fields filled |
| `clear_value()` | butCreate only | Clears invalid field values |
| `validate_projname()` | butCreate only | Project name format/length |
| `validate_bussjustname()` | butCreate only | Business justification |
| `validate_date()` | butCreate only | Date field validity |
| `validateAmount()` | butCreate, butResubmit | Amount field validity |
| `validateAttachment()` | butCreate, butResubmit | Required attachment present |

---

## Dialogs Opened by Footer Buttons

| Button | Dialog Opened |
|--------|--------------|
| butDelete | diaDelDraft |
| butCreate | diaCreate |
| butChange | diaChange (via CHECK AJAX) |
| butSubmitWthErr | diaSubmitWthErr |
| butSubmit | diaSubmit (via CHECK AJAX) |
| butReject | diaReject |
| butClose | diaClose |
| butResubmit | diaReSubmit |

---

## Notes
- All buttons are in `Right` content aggregation of `footerPageCCreate` bar
- Visibility of each button controlled by `GS_PROJ_FORM` binding — backend sets which buttons are visible per workflow state
- `butCreate` has the deepest validation chain — 7 nested checks before opening dialog
- `butChange` and `butSubmit` both route through `getOnlineCheck()` (CHECK AJAX) — dialog opened by CHECK ajaxSuccess, not directly
- `butSave` reads `rbITLYes` radio button state before saving — sets `ITL = "Y"` or `"N"` on model
- `butClose` text is "Withdraw" despite button name being butClose
