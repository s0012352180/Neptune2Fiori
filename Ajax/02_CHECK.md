# AJAX: CHECK
**App:** ZDCI_NAD_PROJECT_REQUEST
**Purpose:** Validates the form before submit — checks program state and routes to correct dialog.

## Parameters
| Direction | Name | Type / Notes |
|-----------|------|--------------|
| INPUT | _(see screenshot in source doc)_ | Form data sent for validation |
| OUTPUT | PROGRAM_STATE | "E" = Error, else OK |
| OUTPUT | PROGRAM_MESSAGE | Error message text if state = "E" |

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
} else if (
    modelUrlParameters.oData.MODE === "EDIT" &&
    modelcntr_formdetails.oData.APPR_STEP === "BUDGET_MANAGER"
) {
    diaChange.open();
} else {
    diaSubmit.open();
}
```

## ajaxError
```js
oApp.setBusy(false);
showMessage(txtConnectionFailure.getText(), sap.ui.core.ValueState.Error);
```

## Notes
- If PROGRAM_STATE = "E" → show error message
- If mode is EDIT and approver step is BUDGET_MANAGER → open Change dialog
- Otherwise → open Submit dialog
- Uses `modelProgramState`, `modelUrlParameters`, `modelcntr_formdetails` models
