# AJAX: DELETE (DeleteDraft)
**App:** ZDCI_NAD_PROJECT_REQUEST
**Purpose:** Deletes a draft form record.

## Parameters
| Direction | Name | Type / Notes |
|-----------|------|--------------|
| INPUT | _(see screenshot in source doc)_ | Draft identifier |
| OUTPUT | PROGRAM_STATE | "E" = Error |
| OUTPUT | PROGRAM_MESSAGE | Error message if state = "E" |

> ⚠️ Full parameter list captured as screenshots — fill in from Neptune designer.

## ajaxSuccess
```js
oApp.setBusy(false);
if (modelProgramState.oData.PROGRAM_STATE &&
    modelProgramState.oData.PROGRAM_STATE.trim() === "E") {
    discoveryFiori.showMessage(
        modelProgramState.oData.PROGRAM_MESSAGE,
        sap.ui.core.ValueState.Error
    );
} else {
    discoveryFiori.showMessage(
        txtDeleteDraft.getText(),
        sap.ui.core.ValueState.Success
    );
    dirty.setDirty(false);
    dc.sendToParent({ event: "form-saved" });
    dc.goBack();
}
```

## ajaxError
```js
oApp.setBusy(false);
showMessage(txtConnectionFailure.getText(), sap.ui.core.ValueState.Error);
```

## Notes
- On success: checks PROGRAM_STATE for backend errors first
- Clean delete: shows success toast `txtDeleteDraft`, clears dirty flag, notifies parent, navigates back
- Uses `dirty.setDirty(false)` to prevent unsaved-changes prompt
