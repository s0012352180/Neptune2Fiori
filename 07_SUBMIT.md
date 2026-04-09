# AJAX: SUBMIT
**App:** ZDCI_NAD_PROJECT_REQUEST
**Purpose:** Submits the form for approval workflow.

## Parameters
| Direction | Name | Type / Notes |
|-----------|------|--------------|
| INPUT | _(see screenshot in source doc)_ | Complete form data |
| OUTPUT | PROGRAM_STATE | "E" = Error |
| OUTPUT | PROGRAM_MESSAGE | Error message text |
| OUTPUT | _(more — see screenshot)_ | System messages, approval flags |

> ⚠️ Full parameter list captured as screenshots — fill in from Neptune designer.

## ajaxSuccess
```js
oApp.setBusy(false);
if (modelProgramState.oData.PROGRAM_STATE &&
    modelProgramState.oData.PROGRAM_STATE.trim() === "E") {
    if (Array.isArray(modelsysMessage.oData) && modelsysMessage.oData.length) {
        diaMessages.open();
    } else {
        discoveryFiori.showMessage(
            modelProgramState.oData.PROGRAM_MESSAGE,
            sap.ui.core.ValueState.Error
        );
    }
} else {
    if (!modeloPageProj.oData.PROJ_FLGRP_0004) {
        sap.m.MessageToast.show("Project Code Request form approved Successfully");
    } else {
        sap.m.MessageToast.show(txtFinalApproval.getText());
    }
}
dirty.setDirty(false);
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

## ajaxError
```js
oApp.setBusy(false);
discoveryFiori.showMessage(
    txtConnectionFailure.getText(),
    sap.ui.core.ValueState.Error
);
```

## Notes
- Error path: if system messages exist → open `diaMessages` dialog, else show PROGRAM_MESSAGE
- Success path: if PROJ_FLGRP_0004 not set → hardcoded approval toast, else i18n `txtFinalApproval`
- Always: clear dirty flag, notify parent, go back
- Models used: `modelProgramState`, `modelsysMessage`, `modeloPageProj`
