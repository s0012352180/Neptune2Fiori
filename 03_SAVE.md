# AJAX: SAVE
**App:** ZDCI_NAD_PROJECT_REQUEST
**AJAX Name:** SaveAsDraft
**AJAX ID:** SAVE
**Purpose:** Saves the form as a draft.

---

## Parameters (Active only — Send or Receive checked)

| Model | Field Type | Binding | Send | Receive | Description |
|-------|-----------|---------|------|---------|-------------|
| cntr_formdetails | FlexBox | GS_FORMDETAILS | ☑ | ☑ | |
| cntr_stepdetails | FlexBox | GS_STEPDETAIL | ☑ | ☑ | |
| listAttachments | List | GT_ATTACHMENTS | ☑ | ☑ | |
| oPageProj | Page | GS_PROJ_FORM | ☑ | ☑ | |
| ProgramState | FlexBox | WA_PROGRAM_STATE | ☐ | ☑ | |
| sectionAttachments | ObjectPageSection | GS_ATTACHMENT | ☑ | ☐ | |
| tabMultiCC | Table | GT_ADDITIONAL_CCODE | ☑ | ☐ | Table for multiple CC |

---

## Send (INPUT to backend)
| Model | Binding |
|-------|---------|
| cntr_formdetails | GS_FORMDETAILS |
| cntr_stepdetails | GS_STEPDETAIL |
| listAttachments | GT_ATTACHMENTS |
| oPageProj | GS_PROJ_FORM |
| sectionAttachments | GS_ATTACHMENT |
| tabMultiCC | GT_ADDITIONAL_CCODE |

## Receive (OUTPUT from backend)
| Model | Binding |
|-------|---------|
| cntr_formdetails | GS_FORMDETAILS |
| cntr_stepdetails | GS_STEPDETAIL |
| listAttachments | GT_ATTACHMENTS |
| oPageProj | GS_PROJ_FORM |
| ProgramState | WA_PROGRAM_STATE |

---

## ajaxSuccess
```js
setTimeout(function() {
    if (modeloPageProj.oData.RESUBMIT) {
        dc.goBack();
    } else {
        getOnlineGetDetail();
    }
}, 200);
sap.m.MessageToast.show(txtSaveDraft.getText());
```

## ajaxError
```js
getOnlineDequeueForm();
setTimeout(function() {
    oApp.setBusy(false);
    jQuery.sap.require("sap.m.MessageToast");
    sap.m.MessageToast.show(txtTranslateESaveAsDraft.getText());
    getOnlineoTreeTable();
    oApp.back();
}, 100);
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

---

## Notes
- Success: if `RESUBMIT` flag set → go back, else reload detail via `getOnlineGetDetail()`
- Shows `txtSaveDraft` toast on success regardless of RESUBMIT flag
- Error: dequeues form lock → shows `txtTranslateESaveAsDraft` toast → refreshes tree table → navigates back
- Error path also sends `form-saved` to parent — form always closes cleanly
- i18n keys: `txtSaveDraft`, `txtTranslateESaveAsDraft`
- Helpers: `getOnlineGetDetail()`, `getOnlineDequeueForm()`, `getOnlineoTreeTable()`
