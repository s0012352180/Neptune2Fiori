# AJAX: REJECT
**App:** ZDCI_NAD_PROJECT_REQUEST
**AJAX Name:** REJECT
**AJAX ID:** REJECT
**Purpose:** Rejects the form request and sends it back to the initiator / requestor.

---

## Parameters (Active only — Send or Receive checked)

| Model | Field Type | Binding | Send | Receive | Description |
|-------|-----------|---------|------|---------|-------------|
| cntr_formdetails | FlexBox | GS_FORMDETAILS | ☑ | ☐ | |
| cntr_stepdetails | FlexBox | GS_STEPDETAIL | ☑ | ☐ | |
| diaReject | Dialog | WA_COMMENTS | ☑ | ☐ | Rejection comments |
| oPageProj | Page | GS_PROJ_FORM | ☑ | ☐ | |
| tabMultiCC | Table | GT_ADDITIONAL_CCODE | ☑ | ☐ | Table for multiple CC |

---

## Send (INPUT to backend)
| Model | Binding |
|-------|---------|
| cntr_formdetails | GS_FORMDETAILS |
| cntr_stepdetails | GS_STEPDETAIL |
| diaReject | WA_COMMENTS |
| oPageProj | GS_PROJ_FORM |
| tabMultiCC | GT_ADDITIONAL_CCODE |

## Receive (OUTPUT from backend)
| Model | Binding |
|-------|---------|
| ProgramState | WA_PROGRAM_STATE |

---

## ajaxSuccess
```js
// getOnlineDequeueForm(); -- commented out
oApp.setBusy(false);
if (modelProgramState.oData.PROGRAM_STATE &&
    modelProgramState.oData.PROGRAM_STATE.trim() === "E") {
    // Output errors
    discoveryFiori.showMessage(
        modelProgramState.oData.PROGRAM_MESSAGE,
        sap.ui.core.ValueState.Error
    );
} else {
    sap.m.MessageToast.show(txtRejected.getText());
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

---

## Notes
- All parameters are Send only — nothing returned except `WA_PROGRAM_STATE` for error check
- Success: checks `PROGRAM_STATE` first — if "E" show backend error message, stay on page
- Clean reject: shows `txtRejected` toast, clears dirty flag, notifies parent, navigates back
- `getOnlineDequeueForm()` is commented out in success — dequeue not needed after reject
- i18n keys: `txtRejected`, `txtConnectionFailure`
- Model used for error check: `modelProgramState`
